/* Add movable algorithm pointer arrows such as slow/fast, lo/mid/hi, or prev/cur/next. ```javascript */

// dsa-pointers-plus
// Adds draggable pointer arrows for common algorithm traces.

const presets = {
  "i, j": ["i", "j"],
  "L, R": ["L", "R"],
  "left, right": ["left", "right"],
  "slow, fast": ["slow", "fast"],
  "prev, cur, next": ["prev", "cur", "next"],
  "lo, mid, hi": ["lo", "mid", "hi"],
  "start, end": ["start", "end"],
  "front, back": ["front", "back"],
  "parent, child": ["parent", "child"],
  "DFS/BFS": ["seen", "queue", "current"],
  "Custom": null,
};

const name = await utils.suggester(Object.keys(presets), Object.keys(presets), "Pointer labels");
if (!name) return;
let labels = presets[name];
if (!labels) {
  const raw = await utils.inputPrompt("Custom labels", "e.g. anchor,runner");
  if (!raw) return;
  labels = raw.split(",").map((x) => x.trim()).filter(Boolean);
}

const direction = await utils.suggester(["Point up", "Point down", "Point right", "Point left"], ["up", "down", "right", "left"], "Arrow direction");
if (!direction) return;

const colors = ["#2f9e44", "#e03131", "#1971c2", "#f08c00", "#7048e8", "#0c8599"];
const before = ea.getElements().length;
ea.style.roughness = 0;
ea.style.strokeWidth = 2;
ea.style.fontFamily = 3;
ea.style.fontSize = 18;
ea.style.endArrowHead = "arrow";

let offset = 0;
const GAP = 44;
const LEN = 34;

for (let idx = 0; idx < labels.length; idx++) {
  const label = labels[idx];
  const color = colors[idx % colors.length];
  ea.style.strokeColor = color;
  const m = ea.measureText(label);
  const width = Math.max(34, m.width + 8);
  let arrowId, textId;

  if (direction === "up") {
    arrowId = ea.addArrow([[offset + width / 2, LEN], [offset + width / 2, 0]], {});
    textId = ea.addText(offset, LEN + 6, label, { width, textAlign: "center" });
  } else if (direction === "down") {
    arrowId = ea.addArrow([[offset + width / 2, 0], [offset + width / 2, LEN]], {});
    textId = ea.addText(offset, -24, label, { width, textAlign: "center" });
  } else if (direction === "right") {
    arrowId = ea.addArrow([[offset, 0], [offset + LEN, 0]], {});
    textId = ea.addText(offset, 8, label, { width, textAlign: "center" });
  } else {
    arrowId = ea.addArrow([[offset + LEN, 0], [offset, 0]], {});
    textId = ea.addText(offset, 8, label, { width, textAlign: "center" });
  }

  ea.addToGroup([arrowId, textId]);
  offset += Math.max(width, LEN) + GAP;
}

const ids = ea.getElements().slice(before).map((e) => e.id);
if (ids.length) ea.addToGroup(ids);
await ea.addElementsToView(true, false);

