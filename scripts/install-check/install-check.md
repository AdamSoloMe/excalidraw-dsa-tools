/* Draws a tiny install-check card to confirm Script Engine can run local scripts. ```javascript */
const x = 0;
const y = 0;
ea.style.strokeColor = "#1e1e1e";
ea.style.backgroundColor = "#e7f5ff";
ea.style.fillStyle = "solid";
ea.style.roughness = 0;
ea.style.strokeWidth = 2;
ea.addRect(x, y, 360, 100);
ea.style.backgroundColor = "transparent";
ea.style.fontSize = 22;
ea.style.fontFamily = 3;
ea.addText(x + 20, y + 28, "DSA Kit Plus installed ✅", { width: 320 });
await ea.addElementsToView(false, false);
