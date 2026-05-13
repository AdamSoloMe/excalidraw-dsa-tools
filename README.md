# DSA Excalidraw Toolkit

A visual algorithm-tracing toolkit for Obsidian Excalidraw.

DSA practice is easier when the diagram does not slow you down. This toolkit lets you paste common algorithm inputs into an Excalidraw canvas and turn them into editable visual structures in seconds. Arrays, grids, trees, linked lists, stacks, queues, hash maps, hash sets, DP tables, pointers, color states, and reasoning cards all live directly inside your Obsidian notes.

The goal is simple: spend less time drawing setup by hand and more time understanding the algorithm.

---

## What it does

```text
input → diagram → pointers → trace states → reasoning card
```

Use the toolkit to:

- Convert raw DSA inputs into editable Excalidraw diagrams
- Create blank templates for tracing algorithms manually
- Add pointer labels for common patterns
- Mark nodes, cells, windows, visited states, answers, and invalid branches
- Reset highlights between iterations
- Add compact reasoning cards for invariants, transitions, complexity, and edge cases
- Build visual LeetCode notes directly inside Obsidian

---

## Supported data structures

### Arrays

Paste an array and render it as an editable row of cells.

```text
[3, 1, 4, 1, 5]
```

Great for:

- Two pointers
- Sliding window
- Prefix sums
- Sorting
- Binary search
- Greedy scans

---

### Matrices and grids

Paste a nested array and render it as a grid.

```text
[[1, 2, 3], [4, 5, 6], [7, 8, 9]]
```

Great for:

- BFS / DFS grid traversal
- Islands problems
- Dynamic programming tables
- Matrix search
- Flood fill

---

### Linked lists

Paste an arrow-style list and render connected nodes.

```text
1 -> 2 -> 3 -> 4
```

Great for:

- Reversal
- Cycle detection
- Merging
- Removing nodes
- Fast/slow pointer tracing

---

### Binary trees

Paste a level-order tree input and render a tree diagram.

```text
[1, 2, 3, null, 4, 5, 6]
```

Great for:

- BFS
- DFS
- Tree recursion
- Lowest common ancestor
- Path sum problems
- Level-order traversal

---

### Hash maps

Paste object-style input and render key-value rows.

```text
{"a": 1, "b": 2, "c": 3}
```

Great for:

- Frequency counting
- Index lookup
- Memoization
- Prefix-sum maps
- Grouping problems

---

### Hash sets

Paste set-style input and render set entries.

```text
Set: a, b, c, d
```

Great for:

- Duplicate detection
- Visited tracking
- Membership checks
- Cycle prevention

---

### Stacks

Paste stack-style input and render a vertical stack.

```text
Stack: 5, 4, 3, 2, 1
```

Great for:

- Monotonic stack
- DFS
- Parentheses matching
- Backtracking
- Recursion simulation

---

### Queues

Paste queue-style input and render a queue.

```text
Queue: A, B, C, D
```

Great for:

- BFS
- Level-order traversal
- Sliding-window queues
- Topological sort

---

## Blank scaffolds

Use scaffolds when you want an empty structure to fill in while tracing.

Available scaffolds:

- Array
- Grid / matrix
- DP table
- Hash map
- Hash set
- Stack
- Queue
- Call stack

These are helpful when the input alone is not enough and you want to build the state step by step.

---

## Pointer presets

Add labeled arrows for common algorithm variables.

Built-in presets:

- `i, j`
- `L, R`
- `left, right`
- `slow, fast`
- `prev, cur, next`
- `lo, mid, hi`
- `start, end`
- `front, back`
- `parent, child`
- `seen, queue, current`
- Custom labels

Pointer directions:

- Up
- Down
- Left
- Right

Use cases:

- `left, right` for sliding window and two pointers
- `slow, fast` for cycle detection
- `lo, mid, hi` for binary search
- `prev, cur, next` for linked-list rewiring
- `front, back` for queues and deques

---

## Trace states

Apply semantic colors to selected elements so each highlight has algorithm meaning.

Available states:

- Current
- Visited
- Window
- Invalid / pruned
- Answer
- Queued
- Stacked
- Clear
- Cycle

Examples:

- Mark the active sliding window as `Window`
- Mark the node being processed as `Current`
- Mark discovered BFS nodes as `Visited`
- Mark frontier nodes as `Queued`
- Mark rejected branches as `Invalid / pruned`
- Mark final result nodes or cells as `Answer`

---

## Reasoning cards

Add a compact note card next to the diagram to capture the algorithm idea.

A good reasoning card includes:

- Pattern / problem
- Invariant
- Transition rule
- Time complexity
- Space complexity
- Edge cases

Example:

```text
Pattern: Sliding Window
Invariant: window contains no duplicate characters
Transition: expand right; shrink left while invalid
Time: O(n)
Space: O(k)
Edges: empty string, all unique, all repeated
```

This turns a diagram into a reusable study note instead of a one-off sketch.

---

## Included scripts

### `install-check`

Draws a small confirmation card so you can verify the scripts are installed correctly.

---

### `dsa-render`

Converts selected text into an editable diagram.

Render targets:

- Auto-detect
- Array
- Matrix / grid
- Linked list
- Binary tree
- Hash map
- Hash set
- Stack
- Queue

Themes:

- Clean
- Pastel
- Dark

---

### `dsa-scaffold`

Creates blank templates for manual tracing.

Templates:

- Array
- Grid / matrix
- DP table
- Hash map
- Hash set
- Stack
- Queue
- Call stack

---

### `dsa-pointers-plus`

Adds labeled pointer arrows to the canvas.

Use it for index variables, linked-list variables, binary-search bounds, queue pointers, recursion markers, and custom labels.

---

### `dsa-state-cycle`

Applies trace-state colors to selected elements.

Use it to mark active cells, visited nodes, sliding windows, pruned branches, answers, queued items, and stack frames.

---

### `dsa-reset-state`

Clears trace colors from selected elements.

Use it when restarting a trace or reusing the same diagram for another pass.

---

### `dsa-trace-card`

Adds a structured reasoning card to the canvas.

Use it to record the invariant, transition rule, time complexity, space complexity, and important edge cases.

---

## Installation

### 1. Enable Script Engine

In Obsidian:

1. Open **Settings**.
2. Go to **Excalidraw**.
3. Enable **Script Engine**.
4. Choose a scripts folder, for example:

```text
Excalidraw/Scripts
```

---

### 2. Copy the scripts

Copy the folders from this repo's `scripts/` directory into your Excalidraw Script Engine folder.

Expected structure:

```text
Excalidraw/Scripts/
├── install-check/
│   ├── install-check.md
│   └── install-check.svg
├── dsa-render/
│   ├── dsa-render.md
│   └── dsa-render.svg
├── dsa-scaffold/
│   ├── dsa-scaffold.md
│   └── dsa-scaffold.svg
├── dsa-pointers-plus/
│   ├── dsa-pointers-plus.md
│   └── dsa-pointers-plus.svg
├── dsa-state-cycle/
│   ├── dsa-state-cycle.md
│   └── dsa-state-cycle.svg
├── dsa-reset-state/
│   ├── dsa-reset-state.md
│   └── dsa-reset-state.svg
└── dsa-trace-card/
    ├── dsa-trace-card.md
    └── dsa-trace-card.svg
```

Each script folder should contain a `.md` script file and a matching `.svg` icon with the same base name.

---

### 3. Reload Obsidian

After copying the scripts:

1. Restart Obsidian, or run **Reload app without saving**.
2. Open an Excalidraw drawing.
3. Run `install-check`.

If a confirmation card appears, the toolkit is installed correctly.

---

## Basic usage

### Render a data structure

1. Open an Excalidraw drawing.
2. Add a text object.
3. Paste an input:

```text
[2, 7, 11, 15]
```

4. Select the text object.
5. Run `dsa-render`.
6. Choose the structure type or use auto-detect.

---

### Add pointers

1. Run `dsa-pointers-plus`.
2. Choose a preset like `left, right`, `slow, fast`, or `lo, mid, hi`.
3. Drag the arrows into position.

---

### Mark state

1. Select cells, nodes, or boxes.
2. Run `dsa-state-cycle`.
3. Choose a state such as `Current`, `Visited`, or `Window`.

---

### Reset state

1. Select highlighted elements.
2. Run `dsa-reset-state`.
3. The selected elements return to their neutral styling.

---

### Add reasoning

1. Run `dsa-trace-card`.
2. Fill in the prompts.
3. Place the card beside the diagram.

---

## Example workflows

### Sliding window

Use:

- `dsa-render` for the array or string
- `dsa-pointers-plus` with `left, right`
- `dsa-state-cycle` with `Window`, `Current`, and `Invalid / pruned`
- `dsa-trace-card` for the invariant

Example invariant:

```text
window contains no duplicate characters
```

---

### Binary search

Use:

- `dsa-render` for the sorted array
- `dsa-pointers-plus` with `lo, mid, hi`
- `dsa-state-cycle` to mark the discarded range
- `dsa-trace-card` for the search invariant

Example invariant:

```text
target, if present, is inside [lo, hi]
```

---

### BFS on a grid

Use:

- `dsa-render` or `dsa-scaffold` for the grid
- `dsa-state-cycle` with `Current`, `Visited`, and `Queued`
- `dsa-scaffold` with `Queue`
- `dsa-trace-card` for the traversal rule

Example invariant:

```text
all queued cells have been discovered but not fully processed
```

---

### Dynamic programming

Use:

- `dsa-scaffold` with `DP Table`
- `dsa-state-cycle` for the current cell and dependencies
- `dsa-trace-card` for recurrence, base cases, and complexity

Example transition:

```text
dp[i] = best answer using the first i items
```

---

### Linked-list rewiring

Use:

- `dsa-render` with `1 -> 2 -> 3 -> 4`
- `dsa-pointers-plus` with `prev, cur, next`
- `dsa-state-cycle` with `Current` and `Answer`
- `dsa-trace-card` for the pointer invariant

Example invariant:

```text
prev points to the reversed prefix; cur points to the remaining suffix
```

---

## Troubleshooting

### Scripts do not appear

Check that each script is in its own folder and has both files:

```text
script-name/script-name.md
script-name/script-name.svg
```

Then restart Obsidian.

---

### Nothing happens when running a script

Run `install-check` first.

If `install-check` works but another script does not, make sure you selected the right thing. For `dsa-render`, select a text object containing input.

---

### Auto-detect picks the wrong structure

Run `dsa-render` again and manually choose the structure type instead of auto-detect.

---

### The diagram looks cramped

Everything generated is editable. Select the elements and move, resize, recolor, or rearrange them like any other Excalidraw drawing.

---

## Quick test inputs

```text
[1, 2, 3, 4]
```

```text
[[0, 1], [1, 0]]
```

```text
1 -> 2 -> 3
```

```text
[1, 2, 3, null, 4]
```

```text
{"a": 1, "b": 2}
```

```text
Set: x, y, z
```

```text
Stack: 3, 2, 1
```

```text
Queue: A, B, C
```

---

## License

MIT License.
