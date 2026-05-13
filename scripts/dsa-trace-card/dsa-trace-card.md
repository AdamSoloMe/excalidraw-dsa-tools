/* Insert a DSA trace card for invariant, transition, complexity, and edge cases. ```javascript */

// dsa-trace-card
// Inserts a compact algorithm-tracing card for Obsidian Excalidraw notes.

const pattern = await utils.inputPrompt("Pattern / problem", "e.g. Sliding Window — Longest Substring");
if (pattern === null) return;
const invariant = await utils.inputPrompt("Invariant", "e.g. window contains no duplicate characters");
if (invariant === null) return;
const transition = await utils.inputPrompt("Transition rule", "e.g. expand right; shrink left while invalid");
if (transition === null) return;
const time = await utils.inputPrompt("Time complexity", "e.g. O(n)");
if (time === null) return;
const space = await utils.inputPrompt("Space complexity", "e.g. O(k)");
if (space === null) return;
const edges = await utils.inputPrompt("Edge cases", "e.g. empty input, duplicates, single item");
if (edges === null) return;

const W = 520;
const P = 18;
const lineH = 28;
const titleH = 44;
const rows = [
  ["Invariant", invariant],
  ["Transition", transition],
  ["Time", time],
  ["Space", space],
  ["Edges", edges],
];
const H = titleH + P + rows.length * lineH + P;

const before = ea.getElements().length;
ea.style.roughness = 0;
ea.style.strokeWidth = 2;
ea.style.fontFamily = 3;
ea.style.roundness = null;
ea.style.strokeColor = "#1e1e1e";
ea.style.backgroundColor = "#f8f9fa";
ea.style.fillStyle = "solid";
ea.addRect(0, 0, W, H);

ea.style.backgroundColor = "#343a40";
ea.addRect(0, 0, W, titleH);
ea.style.strokeColor = "#ffffff";
ea.style.fontSize = 24;
ea.addText(P, 9, pattern || "Algorithm Trace Card", { width: W - 2 * P });

let y = titleH + P;
for (const [k, v] of rows) {
  ea.style.strokeColor = "#1971c2";
  ea.style.fontSize = 18;
  ea.addText(P, y, `${k}:`, { width: 105 });
  ea.style.strokeColor = "#1e1e1e";
  ea.style.fontSize = 18;
  ea.addText(P + 112, y, v || "—", { width: W - P * 2 - 112 });
  y += lineH;
}

const ids = ea.getElements().slice(before).map((e) => e.id);
ea.addToGroup(ids);
await ea.addElementsToView(true, false);

