/* Reset selected element backgrounds to transparent. ```javascript */

// dsa-reset-state
// Clears semantic state colors from selected shapes.

const selected = ea.getViewSelectedElements();
if (selected.length === 0) {
  new Notice("Select shapes to reset.");
  return;
}

ea.copyViewElementsToEAforEditing(selected);
for (const el of ea.getElements()) {
  if (["text", "arrow", "line", "freedraw"].includes(el.type)) continue;
  el.backgroundColor = "transparent";
  el.fillStyle = "solid";
}
await ea.addElementsToView(false, true);

