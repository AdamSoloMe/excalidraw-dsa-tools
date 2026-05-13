/* Cycle selected elements through DSA tracing state colors. ```javascript */

// dsa-state-cycle
// Applies semantic DSA colors to selected shapes.

const selected = ea.getViewSelectedElements();
if (selected.length === 0) {
  new Notice("Select one or more shapes to color.");
  return;
}

const actions = ["Cycle", "Current", "Visited", "Window", "Invalid / Pruned", "Answer", "Queued", "Stacked", "Clear"];
const values = ["cycle", "current", "visited", "window", "invalid", "answer", "queued", "stacked", "clear"];
const action = await utils.suggester(actions, values, "State color");
if (!action) return;

const palette = {
  clear: "transparent",
  current: "#fff3bf",
  visited: "#b2f2bb",
  window: "#d0ebff",
  invalid: "#ffc9c9",
  answer: "#d3f9d8",
  queued: "#e5dbff",
  stacked: "#ffe8cc",
};
const cycle = ["transparent", palette.current, palette.visited, palette.window, palette.invalid, palette.answer];

ea.copyViewElementsToEAforEditing(selected);
for (const el of ea.getElements()) {
  if (["text", "arrow", "line", "freedraw"].includes(el.type)) continue;
  if (action === "cycle") {
    const cur = el.backgroundColor || "transparent";
    const idx = cycle.indexOf(cur);
    el.backgroundColor = cycle[(idx + 1) % cycle.length];
  } else {
    el.backgroundColor = palette[action];
  }
  el.fillStyle = "solid";
}
await ea.addElementsToView(false, true);

