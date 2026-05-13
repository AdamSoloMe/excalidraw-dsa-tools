/* Render selected LeetCode-style input as an editable DSA diagram. ```javascript */

// dsa-render
// Select a text element, run this script, then choose how to visualize it.

const selectedText = ea.getViewSelectedElements().filter((e) => e.type === "text");
if (selectedText.length === 0) {
  new Notice("Select a text element containing DSA input first.");
  return;
}

const source = selectedText[0];
const sourceText = (source.rawText || source.text || "").trim();
if (!sourceText) {
  new Notice("Selected text is empty.");
  return;
}

const modes = [
  "Auto-detect",
  "Array",
  "Matrix / Grid",
  "Linked List",
  "Binary Tree (level order)",
  "Hash Map",
  "Hash Set",
  "Stack",
  "Queue",
];
const modeValues = ["auto", "array", "matrix", "linkedlist", "tree", "hashmap", "hashset", "stack", "queue"];
let mode = await utils.suggester(modes, modeValues, "Visualize selected text as...");
if (!mode) return;

const themeNames = ["Clean", "Pastel", "Dark"];
const themeValues = ["clean", "pastel", "dark"];
const theme = (await utils.suggester(themeNames, themeValues, "Visual style")) || "clean";

const COLORS = {
  clean: {
    stroke: "#1e1e1e",
    muted: "#868e96",
    fill: "transparent",
    header: "#343a40",
    headerText: "#ffffff",
    accent: "#1971c2",
    soft: "#e7f5ff",
  },
  pastel: {
    stroke: "#364fc7",
    muted: "#748ffc",
    fill: "#edf2ff",
    header: "#dbe4ff",
    headerText: "#1e1e1e",
    accent: "#7048e8",
    soft: "#f3f0ff",
  },
  dark: {
    stroke: "#f8f9fa",
    muted: "#adb5bd",
    fill: "#212529",
    header: "#495057",
    headerText: "#f8f9fa",
    accent: "#74c0fc",
    soft: "#343a40",
  },
}[theme];

const startX = source.x;
const startY = source.y + source.height + 55;
const FONT = 3;

function setBaseStyle() {
  ea.style.strokeColor = COLORS.stroke;
  ea.style.backgroundColor = COLORS.fill;
  ea.style.fillStyle = "solid";
  ea.style.roughness = 0;
  ea.style.strokeWidth = 2;
  ea.style.fontFamily = FONT;
  ea.style.fontSize = 22;
  ea.style.roundness = null;
}

function stripAssignment(input) {
  let s = String(input).trim();
  s = s.replace(/^\s*(?:const|let|var)?\s*[A-Za-z_$][\w$]*\s*=\s*/, "");
  s = s.replace(/;\s*$/, "");
  return s.trim();
}

function normalizeQuotes(s) {
  return s.replace(/[“”]/g, '"').replace(/[‘’]/g, "'");
}

function splitTopLevel(input) {
  const out = [];
  let current = "";
  let depth = 0;
  let quote = null;
  let escape = false;
  for (const ch of input) {
    if (escape) {
      current += ch;
      escape = false;
      continue;
    }
    if (ch === "\\") {
      current += ch;
      escape = true;
      continue;
    }
    if (quote) {
      current += ch;
      if (ch === quote) quote = null;
      continue;
    }
    if (ch === '"' || ch === "'") {
      quote = ch;
      current += ch;
      continue;
    }
    if (ch === "[" || ch === "{" || ch === "(") depth++;
    if (ch === "]" || ch === "}" || ch === ")") depth--;
    if (ch === "," && depth === 0) {
      out.push(current.trim());
      current = "";
    } else {
      current += ch;
    }
  }
  if (current.trim().length > 0) out.push(current.trim());
  return out;
}

function unboxOuter(s, left, right) {
  s = s.trim();
  if (s.startsWith(left) && s.endsWith(right)) return s.slice(1, -1).trim();
  return s;
}

function parseAtom(v) {
  let s = String(v).trim();
  if ((s.startsWith('"') && s.endsWith('"')) || (s.startsWith("'") && s.endsWith("'"))) {
    return s.slice(1, -1);
  }
  if (/^null$/i.test(s)) return null;
  if (/^undefined$/i.test(s)) return null;
  if (/^true$/i.test(s)) return true;
  if (/^false$/i.test(s)) return false;
  return s;
}

function valueText(v) {
  if (v === null || v === undefined) return "null";
  return String(v);
}

function parseList(input) {
  let s = normalizeQuotes(stripAssignment(input));
  s = unboxOuter(s, "[", "]");
  s = unboxOuter(s, "{", "}");
  if (!s) return [];
  return splitTopLevel(s).map(parseAtom);
}

function stripKnownListPrefix(input, labels = []) {
  let s = normalizeQuotes(stripAssignment(input)).trim();

  // Support wrappers commonly typed in notes: Set(...), HashSet(...), new Set([...]).
  const labelPattern = labels.length ? labels.join("|") : "[A-Za-z_$][\\w$]*(?:\\s+[A-Za-z_$][\\w$]*)?";
  s = s.replace(new RegExp("^new\\s+(?:" + labelPattern + ")\\s*\\((.*)\\)\\s*$", "i"), "$1");
  s = s.replace(new RegExp("^(?:" + labelPattern + ")\\s*\\((.*)\\)\\s*$", "i"), "$1");

  // Strip labels like Set:, HashSet:, Stack:, Queue:, Array:, or seen: when a structure mode is chosen.
  const explicitPrefix = new RegExp("^\\s*(?:" + labelPattern + ")\\s*[:=]\\s*", "i");
  s = s.replace(explicitPrefix, "").trim();

  // Then strip brackets/braces if the remaining payload uses them.
  s = unboxOuter(s, "[", "]");
  s = unboxOuter(s, "{", "}");
  return s.trim();
}

function parsePrefixedList(input, labels = []) {
  const s = stripKnownListPrefix(input, labels);
  if (!s) return [];
  return splitTopLevel(s).map(parseAtom);
}

function parseHashSet(input) {
  return parsePrefixedList(input, ["hash\\s*set", "hashset", "set", "seen"]);
}

function parseStack(input) {
  return parsePrefixedList(input, ["stack"]);
}

function parseQueue(input) {
  return parsePrefixedList(input, ["queue"]);
}

function normalizeArrowSyntax(s) {
  return String(s)
    .replace(/[‐‑‒–—―−]/g, "-")
    .replace(/[→➔➜➝➞➟➠➡⟶⇢⇒⭢⟹↦]/gu, "->")
    .replace(/=+\s*>/g, "->")
    .replace(/-+\s*>/g, "->");
}

function parseLinkedList(input) {
  let s = normalizeArrowSyntax(normalizeQuotes(stripAssignment(input)).trim());
  s = s.replace(/^\s*(?:linked\s*list|linkedlist|list|ll)\s*[:=]\s*/i, "");

  // Bracketed LeetCode-style lists still work: [1,2,3,4]
  if (/^\[.*\]$/.test(s)) return parseList(s);

  // Arrow notation: 1 -> 2 -> 3 -> 4, A → B → C, 1 > 2 > 3, etc.
  if (s.includes("->")) {
    return s.split(/\s*->\s*/).map((part) => part.trim()).filter(Boolean).map(parseAtom);
  }

  // Some fonts/plugins collapse arrows visually, or notes may use plain greater-than separators.
  if (/\S\s*>\s*\S/.test(s)) {
    const gtParts = s.split(/\s*>\s*/).map((part) => part.trim()).filter(Boolean);
    if (gtParts.length > 1) return gtParts.map(parseAtom);
  }

  // Comma/space fallback for quick notes: 1,2,3 or 1 2 3
  if (s.includes(",")) return splitTopLevel(s).map(parseAtom);
  return s.split(/\s+/).filter(Boolean).map(parseAtom);
}

function tryJSON(input) {
  const s = normalizeQuotes(stripAssignment(input));
  try {
    return JSON.parse(s);
  } catch (_) {
    try {
      return JSON.parse(s.replace(/'/g, '"'));
    } catch (_) {
      return null;
    }
  }
}

function parseMatrix(input) {
  const parsed = tryJSON(input);
  if (Array.isArray(parsed) && parsed.every(Array.isArray)) return parsed;

  let s = normalizeQuotes(stripAssignment(input)).trim();
  s = unboxOuter(s, "[", "]");
  const rows = [];
  for (const row of splitTopLevel(s)) {
    rows.push(parseList(row));
  }
  return rows.filter((r) => r.length > 0);
}

function parsePairs(input) {
  const parsed = tryJSON(input);
  if (parsed && !Array.isArray(parsed) && typeof parsed === "object") {
    return Object.entries(parsed).map(([k, v]) => [k, valueText(v)]);
  }
  let s = normalizeQuotes(stripAssignment(input)).trim();
  s = unboxOuter(s, "{", "}");
  if (!s) return [];
  const pairs = [];
  for (const part of splitTopLevel(s)) {
    const idx = part.indexOf(":");
    if (idx === -1) {
      pairs.push([parseAtom(part), ""]);
    } else {
      pairs.push([parseAtom(part.slice(0, idx)), parseAtom(part.slice(idx + 1))]);
    }
  }
  return pairs.map(([k, v]) => [valueText(k), valueText(v)]);
}

function detectMode(input) {
  const s = normalizeArrowSyntax(stripAssignment(input).trim());
  if (s.includes("->") || /\S\s*>\s*\S/.test(s) || /^\s*(?:linked\s*list|linkedlist|list|ll)\s*[:=]/i.test(s)) return "linkedlist";
  if (/^\[\s*\[/.test(s)) return "matrix";
  if (/^\{/.test(s) && /:/.test(s)) return "hashmap";
  if (/^\{/.test(s)) return "hashset";
  if (/^\s*(?:hash\s*set|hashset|set|seen)\s*[:=]/i.test(s)) return "hashset";
  if (/^\s*new\s+set\s*\(/i.test(s)) return "hashset";
  if (/^\s*stack\s*[:=]/i.test(s)) return "stack";
  if (/^\s*queue\s*[:=]/i.test(s)) return "queue";
  return "array";
}

if (mode === "auto") mode = detectMode(sourceText);

function addLabel(x, y, text, size = 18, color = COLORS.muted) {
  ea.style.fontSize = size;
  ea.style.strokeColor = color;
  return ea.addText(x, y, text);
}

function addCell(x, y, w, h, text, opts = {}) {
  setBaseStyle();
  ea.style.backgroundColor = opts.fill === undefined ? COLORS.fill : opts.fill;
  ea.style.fontSize = opts.fontSize || 24;
  const before = ea.getElements().length;
  ea.addText(x, y, valueText(text), {
    width: w,
    height: h,
    textAlign: "center",
    textVerticalAlign: "middle",
    box: opts.box || "rectangle",
    boxPadding: 0,
  });
  return ea.getElements().slice(before).map((e) => e.id);
}

function groupSince(before) {
  const ids = ea.getElements().slice(before).map((e) => e.id);
  if (ids.length > 0) ea.addToGroup(ids);
  return ids;
}

function drawArray(values, x, y, label = "Array") {
  const before = ea.getElements().length;
  const W = 62;
  const H = 58;
  addLabel(x, y - 50, label, 20, COLORS.accent);
  for (let i = 0; i < values.length; i++) {
    addLabel(x + i * W, y - 26, String(i), 15, COLORS.muted);
    addCell(x + i * W, y, W, H, values[i]);
  }
  groupSince(before);
}

function drawMatrix(grid, x, y) {
  const before = ea.getElements().length;
  const W = 62;
  const H = 58;
  const rowLabelW = 34;
  const colLabelH = 28;
  const rows = grid.length;
  const cols = Math.max(...grid.map((r) => r.length));
  addLabel(x + rowLabelW, y - 52, "Matrix / Grid", 20, COLORS.accent);
  for (let c = 0; c < cols; c++) addLabel(x + rowLabelW + c * W, y - 26, String(c), 15, COLORS.muted);
  for (let r = 0; r < rows; r++) {
    addLabel(x + 8, y + colLabelH + r * H + 18, String(r), 15, COLORS.muted);
    for (let c = 0; c < cols; c++) {
      addCell(x + rowLabelW + c * W, y + colLabelH + r * H, W, H, grid[r][c] ?? "");
    }
  }
  groupSince(before);
}

async function drawLinkedList(values, x, y) {
  const before = ea.getElements().length;
  const W = 70;
  const H = 58;
  const GAP = 66;
  if (!values || values.length === 0) {
    new Notice("Linked list input produced no nodes.");
    return;
  }

  addLabel(x, y - 45, "Linked List", 20, COLORS.accent);

  setBaseStyle();
  const nodeIds = [];
  for (let i = 0; i < values.length; i++) {
    const nx = x + i * (W + GAP);
    const ny = y;

    setBaseStyle();
    ea.style.backgroundColor = COLORS.soft;
    const rectId = ea.addRect(nx, ny, W, H);
    nodeIds.push(rectId);

    ea.style.backgroundColor = "transparent";
    ea.style.strokeColor = COLORS.stroke;
    ea.style.fontFamily = FONT;
    ea.style.fontSize = 24;
    ea.addText(nx, ny + 14, valueText(values[i]), {
      width: W,
      textAlign: "center",
    });
  }

  setBaseStyle();
  ea.style.strokeColor = COLORS.accent;
  ea.style.endArrowHead = "arrow";
  for (let i = 0; i < values.length - 1; i++) {
    const x1 = x + i * (W + GAP) + W + 10;
    const x2 = x + (i + 1) * (W + GAP) - 10;
    const cy = y + H / 2;
    ea.addArrow([[x1, cy], [x2, cy]], {});
  }

  groupSince(before);
  await ea.addElementsToView(false, false);
}

async function drawTree(values, x, y) {
  const before = ea.getElements().length;
  const tokens = values.map((v) => (String(v).toLowerCase() === "null" ? null : v));
  if (tokens.length === 0 || tokens[0] === null) {
    new Notice("Tree input has no root node.");
    return;
  }

  const nodes = [{ value: tokens[0], level: 0, side: null }];
  const parent = [-1];
  let ti = 1;
  let qi = 0;
  while (ti < tokens.length && qi < nodes.length) {
    const level = nodes[qi].level + 1;
    if (ti < tokens.length && tokens[ti] !== null) {
      parent.push(qi);
      nodes.push({ value: tokens[ti], level, side: "left" });
    }
    ti++;
    if (ti < tokens.length && tokens[ti] !== null) {
      parent.push(qi);
      nodes.push({ value: tokens[ti], level, side: "right" });
    }
    ti++;
    qi++;
  }

  const R = 27;
  const D = R * 2;
  const V_GAP = 105;
  const BASE_GAP = 72;
  const maxLevel = Math.max(...nodes.map((n) => n.level));
  const totalWidth = Math.max(220, Math.pow(2, maxLevel) * (D + BASE_GAP));
  const slots = [];
  const pos = [];
  addLabel(x, y - 45, "Binary Tree", 20, COLORS.accent);

  for (let i = 0; i < nodes.length; i++) {
    if (i === 0) {
      slots[i] = [0, totalWidth];
    } else {
      const [l, r] = slots[parent[i]];
      const mid = (l + r) / 2;
      slots[i] = nodes[i].side === "left" ? [l, mid] : [mid, r];
    }
    const [l, r] = slots[i];
    const cx = x + (l + r) / 2;
    pos[i] = { x: cx - R, y: y + nodes[i].level * (D + V_GAP) };
  }

  const ellipseIds = [];
  for (let i = 0; i < nodes.length; i++) {
    const beforeNode = ea.getElements().length;
    addCell(pos[i].x, pos[i].y, D, D, nodes[i].value, { box: "ellipse", fontSize: 22, fill: COLORS.soft });
    const ellipse = ea.getElements().slice(beforeNode).find((e) => e.type === "ellipse");
    ellipseIds.push(ellipse ? ellipse.id : null);
  }
  await ea.addElementsToView(false, false);

  setBaseStyle();
  for (let i = 1; i < nodes.length; i++) {
    if (ellipseIds[i] && ellipseIds[parent[i]]) {
      ea.connectObjects(ellipseIds[parent[i]], "bottom", ellipseIds[i], "top", { endArrowHead: "arrow" });
    } else {
      const p = pos[parent[i]];
      const c = pos[i];
      ea.addArrow([[p.x + R, p.y + D + 4], [c.x + R, c.y - 4]], {});
    }
  }
  groupSince(before);
  await ea.addElementsToView(false, false);
}

function drawHashMap(pairs, x, y) {
  const before = ea.getElements().length;
  const KEY_W = 130;
  const VAL_W = 130;
  const H = 52;
  addLabel(x, y - 45, "Hash Map", 20, COLORS.accent);
  addCell(x, y, KEY_W, 44, "Key", { fill: COLORS.header, fontSize: 20 });
  addCell(x + KEY_W, y, VAL_W, 44, "Value", { fill: COLORS.header, fontSize: 20 });
  for (let r = 0; r < Math.max(1, pairs.length); r++) {
    const [k, v] = pairs[r] || ["", ""];
    addCell(x, y + 44 + r * H, KEY_W, H, k, { fontSize: 22 });
    addCell(x + KEY_W, y + 44 + r * H, VAL_W, H, v, { fontSize: 22 });
  }
  groupSince(before);
}

function drawHashSet(values, x, y) {
  const before = ea.getElements().length;
  const W = 92;
  const H = 54;
  const cols = Math.min(4, Math.max(1, values.length));
  addLabel(x, y - 45, "Hash Set", 20, COLORS.accent);
  for (let i = 0; i < values.length; i++) {
    const r = Math.floor(i / cols);
    const c = i % cols;
    addCell(x + c * W, y + r * H, W, H, values[i], { fill: COLORS.soft });
  }
  groupSince(before);
}

function drawStack(values, x, y) {
  const before = ea.getElements().length;
  const W = 96;
  const H = 52;
  addLabel(x, y - 45, "Stack", 20, COLORS.accent);
  const n = values.length;
  for (let i = 0; i < n; i++) {
    const visualIndex = n - 1 - i;
    addCell(x, y + i * H, W, H, values[visualIndex]);
  }
  addLabel(x + W + 18, y + 12, "top", 18, COLORS.accent);
  ea.style.strokeColor = COLORS.accent;
  ea.style.endArrowHead = "arrow";
  ea.addArrow([[x + W + 10, y + 25], [x + W - 4, y + 25]], {});
  groupSince(before);
}

function drawQueue(values, x, y) {
  const before = ea.getElements().length;
  drawArray(values, x, y, "Queue");
  addLabel(x, y + 75, "front", 17, COLORS.accent);
  addLabel(x + Math.max(0, values.length - 1) * 62, y + 75, "back", 17, COLORS.accent);
  groupSince(before);
}

setBaseStyle();

if (mode === "matrix") {
  drawMatrix(parseMatrix(sourceText), startX, startY);
} else if (mode === "linkedlist") {
  await drawLinkedList(parseLinkedList(sourceText), startX, startY);
  return;
} else if (mode === "tree") {
  await drawTree(parseList(sourceText), startX, startY);
  return;
} else if (mode === "hashmap") {
  drawHashMap(parsePairs(sourceText), startX, startY);
} else if (mode === "hashset") {
  drawHashSet(parseHashSet(sourceText), startX, startY);
} else if (mode === "stack") {
  drawStack(parseStack(sourceText), startX, startY);
} else if (mode === "queue") {
  drawQueue(parseQueue(sourceText), startX, startY);
} else {
  drawArray(parseList(sourceText), startX, startY);
}

await ea.addElementsToView(false, false);

