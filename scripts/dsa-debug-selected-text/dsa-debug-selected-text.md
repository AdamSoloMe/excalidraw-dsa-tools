/* Show the exact selected text that Script Engine receives. ```javascript */
const selectedText = ea.getViewSelectedElements().filter((e) => e.type === "text");
if (selectedText.length === 0) {
  new Notice("Select a text element first.");
  return;
}
const source = selectedText[0];
const raw = source.rawText || source.text || "";
const codes = Array.from(raw).map((ch) => `${ch} U+${ch.codePointAt(0).toString(16).toUpperCase()}`).join(" | ");
new Notice(`Selected text: ${raw}`);
console.log("DSA debug selected text:", raw);
console.log("DSA debug code points:", codes);
