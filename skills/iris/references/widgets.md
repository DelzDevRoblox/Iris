# Widget Reference (v3.0.0)

Complete catalog of Iris widgets and their APIs, distilled from `lib/Widgets/*.luau` (Iris 3.0.0). Each entry lists the moonwave-documented arguments, events and states. `hasChildren` widgets must be paired with `Iris.End()`.

**Notation:** `arg? = default` means optional argument. `[CONFIG]` means the value falls back to the global config. All widget functions use **positional arguments**; states are the last arguments.

## Window

### Window
- `hasChildren: true`, `hasState: true`
- Arguments (positional):
  1. `title: string?` — window title bar text
  2. `flags: number? = 0` — bit flags using `Iris.WindowFlags`
  3. `size: State<Vector2>?` — window size, default `Vector2.new(400, 300)`
  4. `position: State<Vector2>?` — window position, default staggered
  5. `open: State<boolean>?` — window visibility, default `true`
  6. `collapsed: State<boolean>?` — collapsed state, default `false`
  7. `scrollDistance: State<number>?` — vertical scroll position
- Events: `opened()`, `closed()`, `collapsed()`, `uncollapsed()`, `hovered()`
- States: `size`, `position`, `open`, `collapsed`, `scrollDistance`

The top-level container; every other widget must be a descendant of a window. Cannot contain embedded windows. `Iris.SetFocusedWindow(window)` brings a window to front.

### WindowFlags
```lua
Iris.WindowFlags = {
    NoTitleBar = 1,      -- hide title bar
    NoBackground = 2,    -- hide background colour
    NoCollapse = 4,      -- hide collapsing button
    NoClose = 8,         -- hide close button
    NoMove = 16,         -- disable drag-to-move
    NoScrollbar = 32,    -- disable scrollbar
    NoResize = 64,       -- disable drag-to-resize
    NoNav = 128,         -- unused
    NoMenu = 256,        -- hide the menubar
}
```

### Tooltip
- `hasChildren: false`, `hasState: false`
- Arguments: `text: string`
- Events: `hovered()`

Displays a text label next to the cursor, typically gated on another widget's `hovered()`:
```lua
local text = Iris.Text("?")
if text.hovered() then
    Iris.Tooltip("Helpful explanation")
end
```

## Menus

### MenuBar
- `hasChildren: true`, `hasState: false`
- No arguments. Declares a menubar for the current window; must be called directly under a Window. Add `Iris.Menu(...)` children.

### Menu
- `hasChildren: true`, `hasState: true`
- Arguments: `label: string?`, `open: State<boolean>?`
- Events: `clicked()`, `opened()`, `closed()`, `hovered()`
- States: `open`

A collapsible menu. Under a MenuBar it sits horizontally below the title; nested under another Menu it becomes a submenu with an arrow.

### MenuItem
- `hasChildren: false`, `hasState: false`
- Arguments: `label: string`, `keyCode: Enum.KeyCode?`, `modifierKey: Enum.ModifierKey?`
- Events: `clicked()`, `hovered()`

A button inside a menu. **KeyCode/ModifierKey only display the key name; they do not bind a connection.**

### MenuToggle
- `hasChildren: false`, `hasState: true`
- Arguments: `label: string`, `keyCode: Enum.KeyCode?`, `modifierKey: Enum.ModifierKey?`, `checked: State<boolean>?`
- Events: `checked()`, `unchecked()`, `hovered()`
- States: `value`

A togglable menu item; functionally a checkbox.

## Format / Layout

### Separator
- `hasChildren: false`, `hasState: false`
- No arguments. A horizontal line between widgets (vertical inside SameLine).

### SeparatorText
- `hasChildren: false`, `hasState: false`
- Arguments: `text: string`
- A separator with a text label; good section header when a Tree is too heavy.

### Indent
- `hasChildren: true`, `hasState: false`
- Arguments: `width: number? = Iris._config.IndentSpacing`
- Indents its children.

### SameLine
- `hasChildren: true`, `hasState: false`
- Arguments:
  1. `width: number? = Iris._config.ItemSpacing.X` — horizontal spacing
  2. `verticalAlignment: Enum.VerticalAlignment? = Enum.VerticalAlignment.Top`
  3. `horizontalAlignment: Enum.HorizontalAlignment? = Enum.HorizontalAlignment.Left`
- Positions children in a horizontal row.

### Group
- `hasChildren: true`, `hasState: false`
- No arguments. Groups children into a single layout unit.

## Text

### Text
- `hasChildren: false`, `hasState: false`
- Arguments:
  1. `text: string`
  2. `flags: number? = 0` — bit flags using `Iris.TextFlags`
  3. `color: Color3? = [CONFIG] TextColor`
- Events: `hovered()`

Deprecated aliases `Iris.TextWrapped` and `Iris.TextColored` exist; prefer `Text` with flags/color arguments.

### TextFlags
```lua
Iris.TextFlags = {
    Wrapped = 1,   -- enable text wrapping
    RichText = 2,  -- enable rich text formatting
}
```

### InputText
- `hasChildren: false`, `hasState: true`
- Arguments:
  1. `label: string? = "InputText"`
  2. `textHint: string? = ""` — placeholder text
  3. `flags: number? = 0` — bit flags using `Iris.InputTextFlags`
  4. `text: State<string>?`
- Events: `changed()` (fires when the box loses focus after a change), `hovered()`
- States: `text`

### InputTextFlags
```lua
Iris.InputTextFlags = {
    ReadOnly = 1,
    MultiLine = 2,
}
```

## Basic

### Button
- `hasChildren: false`, `hasState: false`
- Arguments: `label: string?`, `size: UDim2? = UDim2.fromOffset(0, 0)`
- Events: `clicked()`, `rightClicked()`, `doubleClicked()`, `ctrlClicked()`, `hovered()`

### SmallButton
- Same as Button but no padding: Arguments `label: string?`, `size: UDim2?`; same events.

### Checkbox
- `hasChildren: false`, `hasState: true`
- Arguments: `label: string?`, `check: State<boolean>?`
- Events: `checked()`, `unchecked()`, `hovered()`
- States: `value`

### CheckboxFlags
- `hasChildren: false`, `hasState: true`
- Arguments: `label: string?`, `bit: number`, `flags: State<number>?`
- Events: `checked()`, `unchecked()`, `hovered()`
- States: `flags`

A checkbox that toggles a single bit in a flags integer. Useful for multi-flag toggles.

### RadioButton
- `hasChildren: false`, `hasState: true`
- Arguments: `label: string?`, `index: any`, `indexState: State<any>?`
- Events: `selected()`, `unselected()`, `active()` (if its Index equals the state), `changed()`, `hovered()`
- States: `index`

Use multiple RadioButtons sharing the same `indexState` to represent one-of-many values.

## Image

### Image
- `hasChildren: false`, `hasState: false`
- Arguments:
  1. `content: Content` — texture asset id or EditableImage
  2. `size: UDim2` — required size
  3. `rect: Rect?` — source rectangle
  4. `scaleType: Enum.ScaleType? = Enum.ScaleType.Stretch`
  5. `resampleMode: Enum.ResamplerMode? = Enum.ResamplerMode.Default`
  6. `tileSize: UDim2? = UDim2.fromScale(1, 1)` — only for Tile
  7. `sliceCenter: Rect?` — only for Slice
  8. `sliceScale: number? = 1` — only for Slice
- Events: `hovered()`
- `TileSize` only used when `ScaleType = Tile`; `SliceCenter`/`SliceScale` only when `Slice`.

### ImageButton
- Same as Image plus button events: `clicked()`, `rightClicked()`, `doubleClicked()`, `ctrlClicked()`, `hovered()`.

## Trees

### Tree
- `hasChildren: true`, `hasState: true`
- Arguments:
  1. `label: string`
  2. `flags: number? = 0` — bit flags using `Iris.TreeFlags`
  3. `open: State<boolean>?`
- Events: `opened()`, `closed()`, `changed()`, `hovered()`
- States: `open`

### TreeFlags
```lua
Iris.TreeFlags = {
    SpanAvailWidth = 1,  -- title fills horizontal space
    NoIndent = 2,        -- children not indented
    DefaultOpen = 4,     -- initially open if no state provided
}
```

### CollapsingHeader
- `hasChildren: true`, `hasState: true`
- Arguments: `label: string`, `flags: number? = 0`, `open: State<boolean>?`
- Events: `opened()`, `closed()`, `hovered()`
- States: `open`

Same as Tree but a larger title; mainly for first-level organization in a window.

## Tabs

### TabBar
- `hasChildren: true`, `hasState: true`
- Arguments: `index: State<number>?` — current tab index (1-based)
- States: `index`
- Container for `Tab` children; the index controls which tab is active.

### Tab
- `hasChildren: true`, `hasState: true`
- Arguments: `label: string`, `flags: number? = 0`, `shown: State<boolean>?`
- Events: `clicked()`, `hovered()`, `selected()`, `unselected()`, `active()`, `shown()`, `hidden()`
- States: `index` (shared from parent TabBar), `shown`
- Must be directly under a TabBar (the TabBar owns the index; you cannot pass an index state to a Tab).

### TabFlags
```lua
Iris.TabFlags = {
    Hideable = 1,  -- enables the close button
}
```

## Input

Shared argument pattern for all `Input*`/`Drag*`/`Slider*` numeric widgets (Num, Vector2, Vector3, UDim, UDim2, Rect):
1. `label: string? = "Input{type}"`
2. `increment: DataType? = nil`
3. `min: DataType? = nil`
4. `max: DataType? = nil`
5. `format: string | {string}? = [DYNAMIC]` — `string.format` pattern(s)
6. `value: State<DataType>?`
7. `editing: State<boolean>?` — read-only, true while user is editing a field

Without explicit `Format`, Iris infers a reasonable format from Increment/Min/Max (e.g. integers for 1/0/100) and prefixes boxes (`X:`, `Y:`, `Z:` for Vector3). Events: `changed()`, `hovered()`, `editing()`. States: `value`, `editing` (read-only).

- `InputNum` — number. Extra: `flags` not used for InputNum (no NoButtons in v3).
- `InputVector2`, `InputVector3`, `InputUDim`, `InputUDim2`, `InputRect` — respective datatypes.
- `DragNum`, `DragVector2`, `DragVector3`, `DragUDim`, `DragUDim2`, `DragRect` — drag-to-edit fields. Ctrl+click types a value; hold Shift for faster, Alt for slower dragging.
- `SliderNum` (defaults `Increment=1, Min=0, Max=100`), `SliderVector2` (`{1,1}`, `{0,0}`, `{100,100}`), `SliderVector3`, `SliderUDim`, `SliderUDim2`, `SliderRect` — a grip constrained between Min and Max.
- `InputColor3` — args: `label?`, `flags?` (`Iris.InputFlags.UseFloats`, `UseHSV`), `format?`, `color: State<Color3>?`, `editing: State<boolean>?`. States: `color`, `editing`.
- `InputColor4` — args: `label?`, `flags?`, `format?`, `color: State<Color3>?`, `transparency: State<number>?`, `editing: State<boolean>?`. States: `color`, `transparency`, `editing`.

### InputFlags
```lua
Iris.InputFlags = {
    UseFloats = 1,  -- 0..1 floats vs 0..255 ints for Color3/4
    UseHSV = 2,     -- HSV instead of RGB for Color3/4
}
```

## Combo

### Selectable
- `hasChildren: false`, `hasState: true`
- Arguments: `label: string?`, `index: any`, `flags: number? = 0` (uses `Iris.ComboFlags.NoClick`), `indexState: State<any>?`
- Events: `selected()`, `unselected()`, `active()`, `clicked()`, `rightClicked()`, `doubleClicked()`, `ctrlClicked()`, `hovered()`
- States: `index` — a state shared between all selectables in the group.

### Combo
- `hasChildren: true`, `hasState: true`
- Arguments: `label: string?`, `flags: number? = 0`, `index: State<any>?`, `open: State<boolean>?`
- Events: `opened()`, `closed()`, `changed()`, `clicked()`, `hovered()`
- States: `index`, `open`
- Children are `Selectable` items sharing the combo's `index` state.

### ComboFlags
```lua
Iris.ComboFlags = {
    NoClick = 1,    -- prevents the selectable from being clicked
    NoButton = 2,   -- hide dropdown button
    NoPreview = 4,  -- hide preview field
}
```

### ComboArray
- Function form: `Iris.ComboArray(label: string?, array: {any}, flags?: number, index?: State<any>, open?: State<boolean>)`
- Third parameter is the array of choices. Generates the combo and its Selectable children internally (inserts them itself; **don't call `Iris.End()` after it**).

### ComboEnum
- Function form: `Iris.ComboEnum(label: string?, enum: Enum, flags?: number, index?: State<any>, open?: State<boolean>)`
- Third parameter is an Enum. Generates a combo of the enum's items, storing the selected `EnumItem`.

## Plots

### ProgressBar
- `hasChildren: false`, `hasState: true`
- Arguments: `label: string? = "Progress Bar"`, `format: string?`, `progress: State<number>?`
- Events: `hovered()`, `changed()`
- States: `progress`

### PlotLines
- `hasChildren: false`, `hasState: true`
- Arguments:
  1. `label: string? = "Plot Lines"`
  2. `height: number`
  3. `min: number?` — default from values
  4. `max: number?` — default from values
  5. `textOverlay: string? = ""`
  6. `values: State<{number}>?`
  7. `hovered: State<{number}>?` — read-only, hovered segment values
- Events: `hovered()`
- States: `values`, `hovered`

### PlotHistogram
- `hasChildren: false`, `hasState: true`
- Arguments:
  1. `label: string? = "Plot Histogram"`
  2. `height: number?`
  3. `min: number?` — default from values
  4. `max: number?` — default from values
  5. `textOverlay: string? = ""`
  6. `baseline: number? = 0` — where bars start from
  7. `values: State<{number}>?`
  8. `hovered: State<number>?` — read-only, hovered value
- Events: `hovered()`
- States: `values`, `hovered` (read-only)

## Table

- `hasChildren: true`, `hasState: true`
- Arguments:
  1. `numColumns: number` (cannot be changed later without `PushConfig`/`PopConfig`)
  2. `flags: number? = 0` — bit flags using `Iris.TableFlags`
  3. `widths: State<{number}>?` — column widths when `Resizable` (0..1 proportions, or pixels >2 when `FixedWidth`)
- Events: `changed()` (widths changed), `hovered()`
- States: `widths`

### TableFlags
```lua
Iris.TableFlags = {
    Header = 1,            -- display header row
    RowBackground = 2,     -- alternating row backgrounds
    OuterBorders = 4,      -- outer border
    InnerBorders = 8,      -- inner borders
    Resizable = 16,        -- columns resizable
    FixedWidth = 32,       -- fixed pixel widths
    ProportionalWidth = 64,-- minimize column widths individually
    LimitTableWidth = 128, -- cap table width (with FixedWidth)
    Borders = 12,          -- OuterBorders + InnerBorders
}
```

### Column Helpers (must be called directly within the table)
- `Iris.NextColumn()` — next cell, wrapping to next row at last column.
- `Iris.NextRow()` — first column of next row.
- `Iris.SetColumnIndex(index)` — move to column in same row (1..NumColumns).
- `Iris.SetRowIndex(index)` — move to row in same column.
- `Iris.NextHeaderColumn()` / `Iris.SetHeaderColumnIndex(index)` — move within header row (row 0).
- `Iris.SetColumnWidth(index, width)` — set column width via the `widths` state.

To change `NumColumns` later, wrap in `Iris.PushConfig({ columns = numColumns })` / `Iris.PopConfig()` so Iris redraws the table. When growing the column count, keep the `widths` state array at least as long as the new column count.
