/* Insert blank editable DSA scaffolds for tracing algorithms. ```javascript */

// dsa-scaffold
// Inserts blank editable DSA scaffolds for tracing algorithms.

const choices = [
  "Array",
  "Grid / Matrix",
  "DP Table",
  "Hash Map",
  "Hash Set",
  "Stack",
  "Queue",
  "Call Stack",
];
const values = ["array", "grid", "dp", "hashmap", "hashset", "stack", "queue", "callstack"];
const mode = await utils.suggester(choices, values, "Insert blank scaffold...");
if (!mode) return;

const BOX_W = 62;
const BOX_H = 56;
const FONT = 3;
const STROKE = "#1e1e1e";
const MUTED = "#868e96";
const HEADER = "#343a40";
const HEADER_TEXT = "#ffffff";

function base() {
  ea.style.strokeColor = STROKE;
  ea.style.backgroundColor = "transparent";
  ea.style.fillStyle = "solid";
  ea.style.roughness = 0;
  ea.style.strokeWidth = 2;
  ea.style.fontFamily = FONT;
  ea.style.fontSize = 22;
  ea.style.roundness = null;
}
function label(x, y, text, size = 16, color = MUTED) {
  ea.style.fontSize = size;
  ea.style.strokeColor = color;
  ea.addText(x, y, text);
}
function cell(x, y, w, h, text = "", fill = "transparent", size = 22) {
  base();
  ea.style.backgroundColor = fill;
  ea.style.fontSize = size;
  ea.addText(x, y, text, {
    width: w,
    height: h,
    textAlign: "center",
    textVerticalAlign: "middle",
    box: "rectangle",
    boxPadding: 0,
  });
}
function groupAllSince(before) {
  const ids = ea.getElements().slice(before).map((e) => e.id);
  if (ids.length) ea.addToGroup(ids);
}
function parseDims(input, fallbackRows = 1) {
  const parts = String(input || "").split(",").map((x) => parseInt(x.trim(), 10));
  const cols = parts[0];
  const rows = parts.length > 1 ? parts[1] : fallbackRows;
  if (!Number.isFinite(cols) || cols < 1 || !Number.isFinite(rows) || rows < 1) return null;
  return { cols, rows };
}

const before = ea.getElements().length;

if (mode === "array") {
  const input = await utils.inputPrompt("Array length", "e.g. 6");
  const dims = parseDims(input);
  if (!dims) return;
  label(0, -28, "Array", 18, "#1971c2");
  for (let c = 0; c < dims.cols; c++) {
    label(c * BOX_W, 0, String(c));
    cell(c * BOX_W, 28, BOX_W, BOX_H);
  }
} else if (mode === "grid" || mode === "dp") {
  const input = await utils.inputPrompt(mode === "dp" ? "DP dimensions (cols,rows)" : "Grid dimensions (cols,rows)", "e.g. 5,4");
  const dims = parseDims(input, 1);
  if (!dims) return;
  const rowLabelW = 34;
  const top = 30;
  label(rowLabelW, -28, mode === "dp" ? "DP Table" : "Grid", 18, "#1971c2");
  for (let c = 0; c < dims.cols; c++) label(rowLabelW + c * BOX_W, 0, String(c));
  for (let r = 0; r < dims.rows; r++) {
    label(8, top + r * BOX_H + 18, String(r));
    for (let c = 0; c < dims.cols; c++) {
      const fill = mode === "dp" && (r === 0 || c === 0) ? "#e7f5ff" : "transparent";
      cell(rowLabelW + c * BOX_W, top + r * BOX_H, BOX_W, BOX_H, "", fill);
    }
  }
} else if (mode === "hashmap") {
  const input = await utils.inputPrompt("Hash map rows", "e.g. 6");
  const rows = parseInt(input, 10);
  if (!Number.isFinite(rows) || rows < 1) return;
  const W = 126;
  cell(0, 0, W, 42, "Key", HEADER, 20);
  cell(W, 0, W, 42, "Value", HEADER, 20);
  for (let r = 0; r < rows; r++) {
    cell(0, 42 + r * BOX_H, W, BOX_H);
    cell(W, 42 + r * BOX_H, W, BOX_H);
  }
} else if (mode === "hashset") {
  const input = await utils.inputPrompt("Hash set slots", "e.g. 8");
  const n = parseInt(input, 10);
  if (!Number.isFinite(n) || n < 1) return;
  const cols = Math.min(4, n);
  label(0, -28, "Hash Set", 18, "#1971c2");
  for (let i = 0; i < n; i++) {
    const r = Math.floor(i / cols), c = i % cols;
    cell(c * 92, r * 54, 92, 54, "", "#e7f5ff");
  }
} else if (mode === "stack" || mode === "callstack") {
  const input = await utils.inputPrompt(mode === "stack" ? "Stack height" : "Call stack frames", "e.g. 5");
  const n = parseInt(input, 10);
  if (!Number.isFinite(n) || n < 1) return;
  label(0, -28, mode === "stack" ? "Stack" : "Call Stack", 18, "#1971c2");
  for (let i = 0; i < n; i++) cell(0, i * BOX_H, mode === "stack" ? 100 : 220, BOX_H, mode === "callstack" ? `frame ${n - i}` : "");
  label(mode === "stack" ? 118 : 238, 16, "top", 18, "#1971c2");
  ea.style.strokeColor = "#1971c2";
  ea.style.endArrowHead = "arrow";
  ea.addArrow([[mode === "stack" ? 108 : 228, 26], [mode === "stack" ? 94 : 214, 26]], {});
} else if (mode === "queue") {
  const input = await utils.inputPrompt("Queue length", "e.g. 6");
  const n = parseInt(input, 10);
  if (!Number.isFinite(n) || n < 1) return;
  label(0, -28, "Queue", 18, "#1971c2");
  for (let i = 0; i < n; i++) cell(i * BOX_W, 0, BOX_W, BOX_H);
  label(0, BOX_H + 14, "front", 16, "#1971c2");
  label((n - 1) * BOX_W, BOX_H + 14, "back", 16, "#1971c2");
}

groupAllSince(before);
await ea.addElementsToView(true, false);

