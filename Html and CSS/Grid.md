![[Grid.png]]



#### display

-   **`grid`** – generates a block-level grid
-   **`inline-grid`** – generates an inline-level grid

```
.container {
  display: grid | inline-grid;
}
```

#### grid-template-columns  

```
.container {
  grid-template-columns: ...  ...;
  /* e.g. 
      1fr 1fr
      minmax(10px, 1fr) 3fr
      repeat(5, 1fr)
      50px auto 100px 1fr
  */
  grid-template-rows: ... ...;
  /* e.g. 
      min-content 1fr min-content
      100px 1fr max-content
  */
}
```

Grid lines are automatically assigned positive numbers from these assignments (-1 being an alternate for the very last row).

![](https://css-tricks.com/wp-content/uploads/2018/11/template-columns-rows-01.svg)

But you can choose to explicitly name the lines. Note the bracket syntax for the line names:

```
.container {
  grid-template-columns: [first] 40px [line2] 50px [line3] auto [col4-start] 50px [five] 40px [end];
  grid-template-rows: [row1-start] 25% [row1-end] 100px [third-line] auto [last-line];
}
```

![Grid with user named lines](https://css-tricks.com/wp-content/uploads/2018/11/template-column-rows-02.svg)

Note that a line can have more than one name. For example, here the second line will have two names: row1-end and row2-start:

```
.container {
  grid-template-rows: [row1-start] 25% [row1-end row2-start] 25% [row2-end];
}
```

If your definition contains repeating parts, you can use the [`repeat()` notation](https://www.digitalocean.com/community/tutorials/css-css-grid-layout-repeat-notation?utm_medium=content_acq&utm_source=css-tricks&utm_campaign=&utm_content=awareness_bestsellers) to streamline things:

```
.container {
  grid-template-columns: repeat(3, 20px [col-start]);
}
```

Which is equivalent to this:

```
.container {
  grid-template-columns: 20px [col-start] 20px [col-start] 20px [col-start];
}
```

If multiple lines share the same name, they can be referenced by their line name and count.

```
.item {
  grid-column-start: col-start 2;
}
```

The `fr` unit allows you to set the size of a track as a fraction of the free space of the grid container. For example, this will set each item to one third the width of the grid container:

```
.container {
  grid-template-columns: 1fr 1fr 1fr;
}
```

The free space is calculated _after_ any non-flexible items. In this example the total amount of free space available to the `fr` units doesn’t include the 50px:

```
.container {
  grid-template-columns: 1fr 50px 1fr 1fr;
}
```

#### grid-template-areas

Defines a grid template by referencing the names of the grid areas which are specified with the `[grid-area](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-area)` property. Repeating the name of a grid area causes the content to span those cells. A period signifies an empty cell. The syntax itself provides a visualization of the structure of the grid.

Values:

-   **`<grid-area-name>`** – the name of a grid area specified with [`grid-area`](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-area)
-   **`.`** – a period signifies an empty grid cell
-   **`none`** – no grid areas are defined

```
.container {
  grid-template-areas: 
    "<grid-area-name> | . | none | ..."
    "...";
}
```

Example:

```
.item-a {
  grid-area: header;
}
.item-b {
  grid-area: main;
}
.item-c {
  grid-area: sidebar;
}
.item-d {
  grid-area: footer;
}

.container {
  display: grid;
  grid-template-columns: 50px 50px 50px 50px;
  grid-template-rows: auto;
  grid-template-areas: 
    "header header header header"
    "main main . sidebar"
    "footer footer footer footer";
}
```

That’ll create a grid that’s four columns wide by three rows tall. The entire top row will be composed of the **header** area. The middle row will be composed of two **main** areas, one empty cell, and one **sidebar** area. The last row is all **footer**.

![Example of grid-template-areas](https://css-tricks.com/wp-content/uploads/2018/11/dddgrid-template-areas.svg)

Each row in your declaration needs to have the same number of cells.

You can use any number of adjacent periods to declare a single empty cell. As long as the periods have no spaces between them they represent a single cell.

Notice that you’re not naming lines with this syntax, just areas. When you use this syntax the lines on either end of the areas are actually getting named automatically. If the name of your grid area is **_foo_**, the name of the area’s starting row line and starting column line will be **_foo_\-start**, and the name of its last row line and last column line will be **_foo_\-end**. This means that some lines might have multiple names, such as the far left line in the above example, which will have three names: header-start, main-start, and footer-start.

#### grid-template

A shorthand for setting `[grid-template-rows](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-template-columns-rows)`, `[grid-template-columns](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-template-columns-rows)`, and `[grid-template-areas](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-template-areas)` in a single declaration.

Values:

-   **`none`** – sets all three properties to their initial values
-   **`<grid-template-rows>` / `<grid-template-columns`\>** – sets `[grid-template-columns](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-template-columns-rows)` and `[grid-template-rows](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-template-columns-rows)` to the specified values, respectively, and sets `[grid-template-areas](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-template-areas)` to `none`

```
.container {
  grid-template: none | <grid-template-rows> / <grid-template-columns>;
}
```

It also accepts a more complex but quite handy syntax for specifying all three. Here’s an example:

```
.container {
  grid-template:
    [row1-start] "header header header" 25px [row1-end]
    [row2-start] "footer footer footer" 25px [row2-end]
    / auto 50px auto;
}
```

That’s equivalent to this:

```
.container {
  grid-template-rows: [row1-start] 25px [row1-end row2-start] 25px [row2-end];
  grid-template-columns: auto 50px auto;
  grid-template-areas: 
    "header header header" 
    "footer footer footer";
}
```

Since `grid-template` doesn’t reset the _implicit_ grid properties (`[grid-auto-columns](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-auto-columns-rows)`, `[grid-auto-rows](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-auto-columns-rows)`, and `[grid-auto-flow](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-auto-flow)`), which is probably what you want to do in most cases, it’s recommended to use the `[grid](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid)` property instead of `grid-template`.

#### column-gap  
row-gap  
grid-column-gap  
grid-row-gap

Specifies the size of the grid lines. You can think of it like setting the width of the gutters between the columns/rows.

Values:

-   **`<line-size>`** – a length value

```
.container {
  /* standard */
  column-gap: <line-size>;
  row-gap: <line-size>;

  /* old */
  grid-column-gap: <line-size>;
  grid-row-gap: <line-size>;
}
```

Example:

```
.container {
  grid-template-columns: 100px 50px 100px;
  grid-template-rows: 80px auto 80px; 
  column-gap: 10px;
  row-gap: 15px;
}
```

![Example of grid-column-gap and grid-row-gap](https://css-tricks.com/wp-content/uploads/2018/11/dddgrid-gap.svg)

The gutters are only created _between_ the columns/rows, not on the outer edges.

Note: The `grid-` prefix will be removed and `grid-column-gap` and `grid-row-gap` renamed to `column-gap` and `row-gap`. The unprefixed properties are already supported in Chrome 68+, Safari 11.2 Release 50+, and Opera 54+.

#### gap  
grid-gap

A shorthand for `[row-gap](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-column-row-gap)` and `[column-gap](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-column-row-gap)`

Values:

-   **`<grid-row-gap>` `<grid-column-gap>`** – length values

```
.container {
  /* standard */
  gap: <grid-row-gap> <grid-column-gap>;

  /* old */
  grid-gap: <grid-row-gap> <grid-column-gap>;
}
```

Example:

```
.container {
  grid-template-columns: 100px 50px 100px;
  grid-template-rows: 80px auto 80px; 
  gap: 15px 10px;
}
```

If no `[row-gap](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-column-row-gap)` is specified, it’s set to the same value as `[column-gap](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-column-row-gap)`

**Note**: The `grid-` prefix is deprecated (but who knows, may never actually be removed from browsers). Essentially `grid-gap` renamed to `gap`. The unprefixed property is already supported in Chrome 68+, Safari 11.2 Release 50+, and Opera 54+.

#### justify-items

Aligns grid items along the _inline (row)_ axis (as opposed to `[align-items](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-align-items)` which aligns along the _block (column)_ axis). This value applies to all grid items inside the container.

Values:

-   **`start`** – aligns items to be flush with the start edge of their cell
-   **`end`** – aligns items to be flush with the end edge of their cell
-   **`center`** – aligns items in the center of their cell
-   **`stretch`** – fills the whole width of the cell (this is the default)

```
.container {
  justify-items: start | end | center | stretch;
}
```

Examples:

```
.container {
  justify-items: start;
}
```

![Example of justify-items set to start](https://css-tricks.com/wp-content/uploads/2018/11/justify-items-start.svg)

```
.container {
  justify-items: end;
}
```

![Example of justify-items set to end](https://css-tricks.com/wp-content/uploads/2018/11/justify-items-end.svg)

```
.container {
  justify-items: center;
}
```

![Example of justify-items set to center](https://css-tricks.com/wp-content/uploads/2018/11/justify-items-center.svg)

```
.container {
  justify-items: stretch;
}
```

![Example of justify-items set to stretch](https://css-tricks.com/wp-content/uploads/2018/11/justify-items-stretch.svg)

This behavior can also be set on individual grid items via the `[justify-self](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-justify-self)` property.

#### align-items

Aligns grid items along the _block (column)_ axis (as opposed to `[justify-items](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-justify-items)` which aligns along the _inline (row)_ axis). This value applies to all grid items inside the container.

Values:

-   **`stretch`** – fills the whole height of the cell (this is the default)
-   **`start`** – aligns items to be flush with the start edge of their cell
-   **`end`** – aligns items to be flush with the end edge of their cell
-   **`center`** – aligns items in the center of their cell
-   **`baseline`** – align items [along text baseline](https://codepen.io/chriscoyier/pen/NWvvPRj). There are modifiers to `baseline` — `first baseline` and `last baseline` which will use the baseline from the first or last line in the case of multi-line text.

```
.container {
  align-items: start | end | center | stretch;
}
```

Examples:

```
.container {
  align-items: start;
}
```

![Example of align-items set to start](https://css-tricks.com/wp-content/uploads/2018/11/align-items-start.svg)

```
.container {
  align-items: end;
}
```

![Example of align-items set to end](https://css-tricks.com/wp-content/uploads/2018/11/align-items-end.svg)

```
.container {
  align-items: center;
}
```

![Example of align-items set to center](https://css-tricks.com/wp-content/uploads/2018/11/align-items-center.svg)

```
.container {
  align-items: stretch;
}
```

![Example of align-items set to stretch](https://css-tricks.com/wp-content/uploads/2018/11/align-items-stretch.svg)

This behavior can also be set on individual grid items via the `[align-self](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-align-self)` property.

There are also modifier keywords `safe` and `unsafe` (usage is like `align-items: safe end`). The `safe` keyword means “try to align like this, but not if it means aligning an item such that it moves into inaccessible overflow area”, while `unsafe` will allow moving content into inaccessible areas (“data loss”).

#### place-items

`place-items` sets both the `align-items` and `justify-items` properties in a single declaration.

Values:

-   **`<align-items>` / `<justify-items>`** – The first value sets `align-items`, the second value `justify-items`. If the second value is omitted, the first value is assigned to both properties.

For more details, see [`align-items`](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-align-items) and [`justify-items`](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-justify-items).

This can be very useful for super quick multi-directional centering:

```
.center {
  display: grid;
  place-items: center;
}
```

#### place-content

`place-content` sets both the `align-content` and `justify-content` properties in a single declaration.

Values:

-   **`<align-content>` / `<justify-content>`** – The first value sets `align-content`, the second value `justify-content`. If the second value is omitted, the first value is assigned to both properties.

All major browsers except Edge support the `place-content` shorthand property.

For more details, see [`align-content`](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-align-content) and [`justify-content`](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-justify-content).

#### grid-auto-columns  
grid-auto-rows

Specifies the size of any auto-generated grid tracks (aka _implicit grid tracks_). Implicit tracks get created when there are more grid items than cells in the grid or when a grid item is placed outside of the explicit grid. (see [The Difference Between Explicit and Implicit Grids](https://css-tricks.com/difference-explicit-implicit-grids/))

Values:

-   **`<track-size>`** – can be a length, a percentage, or a fraction of the free space in the grid (using the `[fr](https://css-tricks.com/snippets/css/complete-guide-grid/#fr-unit)` unit)

```
.container {
  grid-auto-columns: <track-size> ...;
  grid-auto-rows: <track-size> ...;
}
```

To illustrate how implicit grid tracks get created, think about this:

```
.container {
  grid-template-columns: 60px 60px;
  grid-template-rows: 90px 90px;
}
```

![Example of 2x2 grid](https://css-tricks.com/wp-content/uploads/2018/11/grid-auto-columns-rows-01.svg)

This creates a 2 x 2 grid.

But now imagine you use `[grid-column](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-column-row)` and `[grid-row](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-column-row)` to position your grid items like this:

```
.item-a {
  grid-column: 1 / 2;
  grid-row: 2 / 3;
}
.item-b {
  grid-column: 5 / 6;
  grid-row: 2 / 3;
}
```

![Example of implicit tracks](https://css-tricks.com/wp-content/uploads/2018/11/grid-auto-columns-rows-02.svg)

We told .item-b to start on column line 5 and end at column line 6, _but we never defined a column line 5 or 6_. Because we referenced lines that don’t exist, implicit tracks with widths of 0 are created to fill in the gaps. We can use `[grid-auto-columns](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-auto-columns-rows)` and `[grid-auto-rows](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-auto-columns-rows)` to specify the widths of these implicit tracks:

```
.container {
  grid-auto-columns: 60px;
}
```

![grid-auto-columns-rows](https://css-tricks.com/wp-content/uploads/2018/11/grid-auto-columns-rows-03.svg)

#### grid-auto-flow

If you have grid items that you don’t explicitly place on the grid, the _auto-placement algorithm_ kicks in to automatically place the items. This property controls how the auto-placement algorithm works.

Values:

-   **`row`** – tells the auto-placement algorithm to fill in each row in turn, adding new rows as necessary (default)
-   **`column`** – tells the auto-placement algorithm to fill in each column in turn, adding new columns as necessary
-   **`dense`** – tells the auto-placement algorithm to attempt to fill in holes earlier in the grid if smaller items come up later

```
.container {
  grid-auto-flow: row | column | row dense | column dense;
}
```

Note that **dense** only changes the visual order of your items and might cause them to appear out of order, which is bad for accessibility.

Examples:

Consider this HTML:

```
<section class="container">
  <div class="item-a">item-a</div>
  <div class="item-b">item-b</div>
  <div class="item-c">item-c</div>
  <div class="item-d">item-d</div>
  <div class="item-e">item-e</div>
</section>
```

You define a grid with five columns and two rows, and set `grid-auto-flow` to `row` (which is also the default):

```
.container {
  display: grid;
  grid-template-columns: 60px 60px 60px 60px 60px;
  grid-template-rows: 30px 30px;
  grid-auto-flow: row;
}
```

When placing the items on the grid, you only specify spots for two of them:

```
.item-a {
  grid-column: 1;
  grid-row: 1 / 3;
}
.item-e {
  grid-column: 5;
  grid-row: 1 / 3;
}
```

Because we set `grid-auto-flow` to `row`, our grid will look like this. Notice how the three items we didn’t place (**item-b**, **item-c** and **item-d**) flow across the available rows:

![Example of grid-auto-flow set to row](https://css-tricks.com/wp-content/uploads/2018/11/grid-auto-flow-01.svg)

If we instead set `grid-auto-flow` to `column`, **item-b**, **item-c** and **item-d** flow down the columns:

```
.container {
  display: grid;
  grid-template-columns: 60px 60px 60px 60px 60px;
  grid-template-rows: 30px 30px;
  grid-auto-flow: column;
}
```

![Example of grid-auto-flow set to column](https://css-tricks.com/wp-content/uploads/2018/11/grid-auto-flow-02.svg)

#### grid

A shorthand for setting all of the following properties in a single declaration: `[grid-template-rows](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-template-columns-rows)`, `[grid-template-columns](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-template-columns-rows)`, `[grid-template-areas](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-template-areas)`, `[grid-auto-rows](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-auto-columns-rows)`, `[grid-auto-columns](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-auto-columns-rows)`, and `[grid-auto-flow](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-auto-flow)` (Note: You can only specify the explicit or the implicit grid properties in a single grid declaration).

Values:

-   **`none`** – sets all sub-properties to their initial values.
-   **`<grid-template>`** – works the same as the `[grid-template](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-template)` shorthand.
-   **`<grid-template-rows> / [ auto-flow && dense? ] <grid-auto-columns>?`** – sets `[grid-template-rows](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-template-columns-rows)` to the specified value. If the `auto-flow` keyword is to the right of the slash, it sets `[grid-auto-flow](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-auto-flow)` to `column`. If the `dense` keyword is specified additionally, the auto-placement algorithm uses a “dense” packing algorithm. If `[grid-auto-columns](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-auto-columns-rows)` is omitted, it is set to `auto`.
-   **`[ auto-flow && dense? ] <grid-auto-rows>? / <grid-template-columns>`** – sets `[grid-template-columns](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-template-columns-rows)` to the specified value. If the `auto-flow` keyword is to the left of the slash, it sets `[grid-auto-flow](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-auto-flow)` to `row`. If the `dense` keyword is specified additionally, the auto-placement algorithm uses a “dense” packing algorithm. If `[grid-auto-rows](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-auto-columns-rows)` is omitted, it is set to `auto`.

Examples:

The following two code blocks are equivalent:

```
.container {
  grid: 100px 300px / 3fr 1fr;
}

.container {
  grid-template-rows: 100px 300px;
  grid-template-columns: 3fr 1fr;
}
```

The following two code blocks are equivalent:

```
.container {
  grid: auto-flow / 200px 1fr;
}

.container {
  grid-auto-flow: row;
  grid-template-columns: 200px 1fr;
}
```

The following two code blocks are equivalent:

```
.container {
  grid: auto-flow dense 100px / 1fr 2fr;
}

.container {
  grid-auto-flow: row dense;
  grid-auto-rows: 100px;
  grid-template-columns: 1fr 2fr;
}
```

And the following two code blocks are equivalent:

```
.container {
  grid: 100px 300px / auto-flow 200px;
}

.container {
  grid-template-rows: 100px 300px;
  grid-auto-flow: column;
  grid-auto-columns: 200px;
}
```

It also accepts a more complex but quite handy syntax for setting everything at once. You specify `[grid-template-areas](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-template-areas)`, `[grid-template-rows](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-template-columns-rows)` and `[grid-template-columns](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-template-columns-rows)`, and all the other sub-properties are set to their initial values. What you’re doing is specifying the line names and track sizes inline with their respective grid areas. This is easiest to describe with an example:

```
.container {
  grid: [row1-start] "header header header" 1fr [row1-end]
        [row2-start] "footer footer footer" 25px [row2-end]
        / auto 50px auto;
}
```

That’s equivalent to this:

```
.container {
  grid-template-areas: 
    "header header header"
    "footer footer footer";
  grid-template-rows: [row1-start] 1fr [row1-end row2-start] 25px [row2-end];
  grid-template-columns: auto 50px auto;    
}
```

#### Properties for the Children  
(Grid Items)

Jump links

-   [](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-display)``[`grid-column-start`](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-column-row-start-end)``
-   [](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-display)``[`grid-column-end`](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-column-row-start-end)``
-   [](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-display)``[`grid-row-start`](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-column-row-start-end)``
-   [](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-display)``[`grid-row-end`](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-column-row-start-end)``
-   [](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-display)``[`grid-column`](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-column-row)``
-   [](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-display)``[`grid-row`](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-column-row)``
-   [](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-display)``[`grid-area`](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-area)``
-   [](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-display)``[`justify-self`](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-justify-self)``
-   [](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-display)``[`align-self`](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-align-self)``
-   [](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-display)``[`place-self`](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-place-self)``

`float`, `display: inline-block`, `display: table-cell`, `vertical-align` and `column-*` properties have no effect on a grid item.

#### grid-column-start  
grid-column-end  
grid-row-start  
grid-row-end

Determines a grid item’s location within the grid by referring to specific grid lines. `grid-column-start`/`grid-row-start` is the line where the item begins, and `grid-column-end`/`grid-row-end` is the line where the item ends.

Values:

-   **`<line>`** – can be a number to refer to a numbered grid line, or a name to refer to a named grid line
-   **`span <number>`** – the item will span across the provided number of grid tracks
-   **`span <name>`** – the item will span across until it hits the next line with the provided name
-   **`auto`** – indicates auto-placement, an automatic span, or a default span of one

```
.item {
  grid-column-start: <number> | <name> | span <number> | span <name> | auto;
  grid-column-end: <number> | <name> | span <number> | span <name> | auto;
  grid-row-start: <number> | <name> | span <number> | span <name> | auto;
  grid-row-end: <number> | <name> | span <number> | span <name> | auto;
}
```

Examples:

```
.item-a {
  grid-column-start: 2;
  grid-column-end: five;
  grid-row-start: row1-start;
  grid-row-end: 3;
}
```

![Example of grid-row/column-start/end](https://css-tricks.com/wp-content/uploads/2018/11/grid-column-row-start-end-01.svg)

```
.item-b {
  grid-column-start: 1;
  grid-column-end: span col4-start;
  grid-row-start: 2;
  grid-row-end: span 2;
}
```

![Example of grid-row/column-start/end](https://css-tricks.com/wp-content/uploads/2018/11/grid-column-row-start-end-02.svg)

If no `grid-column-end`/`grid-row-end` is declared, the item will span 1 track by default.

Items can overlap each other. You can use `z-index` to control their stacking order.

Learn more about the `span` notation in [this article by DigitalOcean](https://www.digitalocean.com/community/tutorials/css-css-grid-layout-span-keyword?utm_medium=content_acq&utm_source=css-tricks&utm_campaign=&utm_content=awareness_bestsellers).

#### grid-column  
grid-row

Shorthand for `[grid-column-start](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-column-row-start-end)` + `[grid-column-end](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-column-row-start-end)`, and `[grid-row-start](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-column-row-start-end)` + `[grid-row-end](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-column-row-start-end)`, respectively.

Values:

-   **`<start-line>` / `<end-line>`** – each one accepts all the same values as the longhand version, including span

```
.item {
  grid-column: <start-line> / <end-line> | <start-line> / span <value>;
  grid-row: <start-line> / <end-line> | <start-line> / span <value>;
}
```

Example:

```
.item-c {
  grid-column: 3 / span 2;
  grid-row: third-line / 4;
}
```

![Example of grid-column/grid-row](https://css-tricks.com/wp-content/uploads/2018/11/grid-column-row.svg)

If no end line value is declared, the item will span 1 track by default.

#### grid-area

Gives an item a name so that it can be referenced by a template created with the `[grid-template-areas](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-template-areas)` property. Alternatively, this property can be used as an even shorter shorthand for `[grid-row-start](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-column-row-start-end)` + `[grid-column-start](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-column-row-start-end)` + `[grid-row-end](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-column-row-start-end)` + `[grid-column-end](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-column-row-start-end)`.

Values:

-   **`<name>`** – a name of your choosing
-   **`<row-start>` / `<column-start>` / `<row-end>` / `<column-end>`** – can be numbers or named lines

```
.item {
  grid-area: <name> | <row-start> / <column-start> / <row-end> / <column-end>;
}
```

Examples:

As a way to assign a name to the item:

```
.item-d {
  grid-area: header;
}
```

As the short-shorthand for `[grid-row-start](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-column-row-start-end)` + `[grid-column-start](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-column-row-start-end)` + `[grid-row-end](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-column-row-start-end)` + `[grid-column-end](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-column-row-start-end)`:

```
.item-d {
  grid-area: 1 / col4-start / last-line / 6;
}
```

![Example of grid-area](https://css-tricks.com/wp-content/uploads/2018/11/grid-area.svg)

#### justify-self

Aligns a grid item inside a cell along the _inline (row)_ axis (as opposed to `[align-self](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-align-self)` which aligns along the _block (column)_ axis). This value applies to a grid item inside a single cell.

Values:

-   **`start`** – aligns the grid item to be flush with the start edge of the cell
-   **`end`** – aligns the grid item to be flush with the end edge of the cell
-   **`center`** – aligns the grid item in the center of the cell
-   **`stretch`** – fills the whole width of the cell (this is the default)

```
.item {
  justify-self: start | end | center | stretch;
}
```

Examples:

```
.item-a {
  justify-self: start;
}
```

![Example of justify-self set to start](https://css-tricks.com/wp-content/uploads/2018/11/justify-self-start.svg)

```
.item-a {
  justify-self: end;
}
```

![alt="Example](https://css-tricks.com/wp-content/uploads/2018/11/justify-self-end.svg)

```
.item-a {
  justify-self: center;
}
```

![Example of justify-self set to center](https://css-tricks.com/wp-content/uploads/2018/11/justify-self-center.svg)

```
.item-a {
  justify-self: stretch;
}
```

![Example of justify-self set to stretch](https://css-tricks.com/wp-content/uploads/2018/11/justify-self-stretch.svg)

To set alignment for _all_ the items in a grid, this behavior can also be set on the grid container via the `[justify-items](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-justify-items)` property.

#### align-self

Aligns a grid item inside a cell along the _block (column)_ axis (as opposed to `[justify-self](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-justify-self)` which aligns along the _inline (row)_ axis). This value applies to the content inside a single grid item.

Values:

-   **`start`** – aligns the grid item to be flush with the start edge of the cell
-   **`end`** – aligns the grid item to be flush with the end edge of the cell
-   **`center`** – aligns the grid item in the center of the cell
-   **`stretch`** – fills the whole height of the cell (this is the default)

```
.item {
  align-self: start | end | center | stretch;
}
```

Examples:

```
.item-a {
  align-self: start;
}
```

![Example of align-self set to start](https://css-tricks.com/wp-content/uploads/2018/11/align-self-start.svg)

```
.item-a {
  align-self: end;
}
```

![Example of align-self set to end](https://css-tricks.com/wp-content/uploads/2018/11/align-self-end.svg)

```
.item-a {
  align-self: center;
}
```

![Example of align-self set to center](https://css-tricks.com/wp-content/uploads/2018/11/align-self-center.svg)

```
.item-a {
  align-self: stretch;
}
```

![Example of align-self set to stretch](https://css-tricks.com/wp-content/uploads/2018/11/align-self-stretch.svg)

To align _all_ the items in a grid, this behavior can also be set on the grid container via the `[align-items](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-align-items)` property.

#### place-self

`place-self` sets both the `align-self` and `justify-self` properties in a single declaration.

Values:

-   **`auto`** – The “default” alignment for the layout mode.
-   **`<align-self>` / `<justify-self>`** – The first value sets `align-self`, the second value `justify-self`. If the second value is omitted, the first value is assigned to both properties.

Examples:

```
.item-a {
  place-self: center;
}
```

![place self set to center](https://css-tricks.com/wp-content/uploads/2018/11/place-self-center.svg)

```
.item-a {
  place-self: center stretch;
}
```

![place set set to center stretch](https://css-tricks.com/wp-content/uploads/2018/11/place-self-center-stretch.svg)

All major browsers except Edge support the `place-self` shorthand property.

## Special Units & Functions

#### fr units

You’ll likely end up using a lot of [fractional units](https://css-tricks.com/introduction-fr-css-unit/) in CSS Grid, like `1fr`. They essentially mean “portion of the remaining space”. So a declaration like:

```
grid-template-columns: 1fr 3fr;
```

Means, loosely, `25% 75%`. Except that those percentage values are much more firm than fractional units are. For example, if you added padding to those percentage-based columns, now you’ve broken 100% width (assuming a `content-box` box model). Fractional units also much more friendly in combination with other units, as you can imagine:

```
grid-template-columns: 50px min-content 1fr;
```

#### Sizing Keywords

When sizing rows and columns, you can use all the [lengths](https://css-tricks.com/the-lengths-of-css/) you are used to, like `px`, rem, %, etc, but you also have keywords:

-   `min-content`: the minimum size of the content. Imagine a line of text like “E pluribus unum”, the min-content is likely the width of the word “pluribus”.
-   `max-content`: the maximum size of the content. Imagine the sentence above, the `max-content` is the length of the whole sentence.
-   `auto`: this keyword is a lot like `fr` units, except that they “lose” the fight in sizing against `fr` units when allocating the remaining space.
-   Fractional units: see above

#### Sizing Functions

-   The `fit-content()` function uses the space available, but never less than `min-content` and never more than `max-content`.
-   The `minmax()` function does exactly what it seems like: it sets a minimum and maximum value for what the length is able to be. This is useful for in combination with relative units. Like you may want a column to be only able to shrink so far. This is [extremely useful and probably what you want](https://css-tricks.com/you-want-minmax10px-1fr-not-1fr/):

```
grid-template-columns: minmax(100px, 1fr) 3fr;
```

-   The `min()` function.
-   The `max()` function.

#### The repeat() Function and Keywords

The `repeat()` function can save some typing:

```
grid-template-columns:
  1fr 1fr 1fr 1fr 1fr 1fr 1fr 1fr;

/* easier: */
grid-template-columns:
  repeat(8, 1fr);

/* especially when: */
grid-template-columns:
  repeat(8, minmax(10px, 1fr));
```

But `repeat()` can get extra fancy when combined with keywords:

-   `auto-fill`: Fit as many possible columns as possible on a row, even if they are empty.
-   `auto-fit:` Fit whatever columns there are into the space. Prefer expanding columns to fill space rather than empty columns.

This bears the most famous snippet in all of CSS Grid and one of the [all-time great CSS tricks](https://css-tricks.com/books/greatest-css-tricks/flexible-grids/):

```
grid-template-columns: 
  repeat(auto-fit, minmax(250px, 1fr));
```

The difference between the keywords is [spelled out in detail here](https://css-tricks.com/auto-sizing-columns-css-grid-auto-fill-vs-auto-fit/).

#### Masonry

An experimental feature of CSS grid is masonry layout. Note that there are [lots of approaches to CSS masonry](https://css-tricks.com/piecing-together-approaches-for-a-css-masonry-layout/), but mostly of them are trickery and either have major downsides or aren’t what you quite expect.

[The spec has an official way](https://drafts.csswg.org/css-grid-3/#masonry-layout) now, and this is behind a flag in Firefox:

```
.container {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-template-rows: masonry;
}
```

[See Rachel’s article](https://www.smashingmagazine.com/native-css-masonry-layout-css-grid/) for a deep dive.

#### Subgrid

Subgrid is an extremely useful feature of grids that allows grid items to have a grid of their own that inherits grid lines from the parent grid.

```
.parent-grid {
  display: grid;
  grid-template-columns: repeat(9, 1fr);
}
.grid-item {
  grid-column: 2 / 7;

  display: grid;
  grid-template-columns: subgrid;
}
.child-of-grid-item {
  /* gets to participate on parent grid! */
  grid-column: 3 / 6;
}
```

This is [only supported in Firefox](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Grid_Layout/Subgrid#browser_compatibility) right now, but it really [needs](http://meyerweb.com/eric/thoughts/2016/01/15/subgrids-considered-essential/) to get everywhere.

It’s also useful to know about `display: contents;`. This is _not_ the same as subgrid, but it can be a useful tool sometimes in a similar fashion.

```
<div class="grid-parent">

  <div class="grid-item"></div>
  <div class="grid-item"></div>

  <ul style="display: contents;">
    <!-- These grid-items get to participate on 
         the same grid!-->
    <li class="grid-item"></li>
    <li class="grid-item"></li>
  </ul>

</div>
```

### CSS Grid browser support

#### Desktop

| Chrome | Firefox | IE | Edge | Safari |
| --- | --- | --- | --- | --- |
| 57 | 52 | 11\* | 16 | 10.1 |

#### Mobile / Tablet

| Android Chrome | Android Firefox | Android | iOS Safari |
| --- | --- | --- | --- |
| 117 | 118 | 117 | 10.3 |

### Fluid columns snippet

Fluid width columns that break into more or less columns as space is available, with no media queries!

```
.grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  /* This is better for small screens, once min() is better supported */
  /* grid-template-columns: repeat(auto-fill, minmax(min(200px, 100%), 1fr)); */
  gap: 1rem;
}
```

### CSS Grid animation

According to the CSS Grid Layout Module Level 1 specification, there are 5 animatable grid properties:

-   `grid-gap`, `grid-row-gap`, `grid-column-gap` as length, percentage, or calc.
-   `grid-template-columns`, `grid-template-rows` as a simple list of length, percentage, or calc, provided the only differences are the values of the length, percentage, or calc components in the list.

As of this writing, only the animation of `(grid-)gap`, `(grid-)row-gap`, `(grid-)column-gap` is implemented in any of the tested browsers.

| Browser | `(grid-)gap`, `(grid-)row-gap`, `(grid-)column-gap` | `grid-template-columns` | `grid-template-rows` |
| --- | --- | --- | --- |
| Firefox | supported ✅ 53+ | supported ✅ 66+ | supported ✅ 66+ |
| Safari 12.0 | not supported ❌ | not supported ❌ | not supported ❌ |
| Chrome | supported ✅ 66+ | not supported ❌ | not supported ❌ |
| Chrome for Android 66+, Opera Mini 33+ | supported ✅ | not supported ❌ | not supported ❌ |
| Edge | supported ✅ 16+ | not supported ❌ | not supported ❌ |

### CSS-Grid tricks!

**Article** on Mar 30, 2020

### [4 CSS Grid Properties (and One Value) for Most of Your Layout Needs](https://css-tricks.com/4-css-grid-properties-and-one-value-for-most-of-your-layout-needs/)

**Article** on Dec 24, 2020

### [A Calendar in Three Lines of CSS](https://css-tricks.com/a-calendar-in-three-lines-of-css/)

**Article** on Nov 17, 2021

### [A Clever Sticky Footer Technique](https://css-tricks.com/a-clever-sticky-footer-technique/)

**Article** on Apr 7, 2020

### [A Grid of Logos in Squares](https://css-tricks.com/a-grid-of-logos-in-squares/)

**Article** on Aug 3, 2020

### [A Lightweight Masonry Solution](https://css-tricks.com/a-lightweight-masonry-solution/)

**Article** on Jul 23, 2020

### [Accordion Rows in CSS Grid](https://css-tricks.com/accordion-rows-in-css-grid/)

**Article** on May 9, 2019

### [A responsive grid layout with no media queries](https://css-tricks.com/a-responsive-grid-layout-with-no-media-queries/)

**Article** on Feb 16, 2022

### [An Auto-Filling CSS Grid With Max Columns of a Minimum Size](https://css-tricks.com/an-auto-filling-css-grid-with-max-columns/)

**Article** on Sep 22, 2022

### [Auto-Sizing Columns in CSS Grid: \`auto-fill\` vs \`auto-fit\`](https://css-tricks.com/auto-sizing-columns-css-grid-auto-fill-vs-auto-fit/)

**Article** on Jun 6, 2017

### [Breaking Out with CSS Grid Explained](https://css-tricks.com/breaking-css-grid-explained/)

**Article** on Jul 31, 2019

### [Bringing CSS Grid to WordPress Layouts](https://css-tricks.com/bringing-css-grid-to-wordpress-layouts/)

**Article** on Mar 27, 2017

### [Building a CSS Grid Overlay](https://css-tricks.com/building-css-grid-overlay/)

**Article** on Jun 24, 2019

### [Building a Conference Schedule with CSS Grid](https://css-tricks.com/building-a-conference-schedule-with-css-grid/)

**Article** on Jun 18, 2020

### [Building a hexagonal grid using CSS grid](https://css-tricks.com/building-a-hexagonal-grid-using-css-grid/)

**Article** on Sep 5, 2017

### [Breaking the Grid](https://css-tricks.com/breaking-the-grid/)

**Article** on Nov 11, 2022

### [CSS Grid and Custom Shapes, Part 1](https://css-tricks.com/css-grid-and-custom-shapes-part-1/)

**Article** on Jul 19, 2020

### [Cool Little CSS Grid Tricks for Your Blog](https://css-tricks.com/cool-little-css-grid-tricks-for-your-blog/)

**Article** on Feb 9, 2018

### [Counting With CSS Counters and CSS Grid](https://css-tricks.com/counting-css-counters-css-grid/)

**Article** on Jun 12, 2018

### [Creating a Bar Graph with CSS Grid](https://css-tricks.com/creating-a-bar-graph-with-css-grid/)

**Article** on Nov 8, 2021

### [CSS Grid Can Do Auto Height Transitions](https://css-tricks.com/css-grid-can-do-auto-height-transitions/)

**Article** on Feb 18, 2017

### [CSS Grid: One Layout, Multiple Ways](https://css-tricks.com/css-grid-one-layout-multiple-ways/)

**Snippet** on Dec 13, 2019

### [CSS Grid Starter Layouts](https://css-tricks.com/snippets/css/css-grid-starter-layouts/)

**Article** on Jan 27, 2022

### [Equal Width Columns in CSS Grid are Kinda Weird](https://css-tricks.com/equal-width-columns-in-css-grid-are-kinda-weird/)

**Article** on Oct 18, 2021

### [Expandable Sections Within a CSS Grid](https://css-tricks.com/expandable-sections-within-a-css-grid/)

**Article** on Aug 8, 2022

### [Exploring CSS Grid’s Implicit Grid and Auto-Placement Powers](https://css-tricks.com/exploring-css-grids-implicit-grid-and-auto-placement-powers/)

**Article** on May 21, 2020

### [Flexbox-like “just put elements in a row” with CSS grid](https://css-tricks.com/flexbox-like-just-put-elements-in-a-row-with-css-grid/)

**Article** on Jun 12, 2019

### [Grid, content re-ordering and accessibility](https://css-tricks.com/grid-content-re-ordering-and-accessibility/)

**Article** on Aug 2, 2022

### [Implicit Grids, Repeatable Layout Patterns, and Danglers](https://css-tricks.com/implicit-grids-repeatable-layout-patterns-and-danglers/)

**Article** on Feb 26, 2019

### [Look Ma, No Media Queries! Responsive Layouts Using CSS Grid](https://css-tricks.com/look-ma-no-media-queries-responsive-layouts-using-css-grid/)

**Article** on Jul 12, 2022

### [Making A Bar Chart with CSS Grid](https://css-tricks.com/making-a-bar-chart-with-css-grid/)

**Article** on Jun 1, 2020

### [Overlapping Header with CSS Grid](https://css-tricks.com/overlapping-header-with-css-grid/)

**Article** on Jun 28, 2021

### [Positioning Overlay Content with CSS Grid](https://css-tricks.com/positioning-overlay-content-with-css-grid/)

**Article** on Oct 1, 2018

### [Preventing a Grid Blowout](https://css-tricks.com/preventing-a-grid-blowout/)

**Article** on Nov 2, 2018

### [Simple Named Grid Areas](https://css-tricks.com/simple-named-grid-areas/)

**Article** on Nov 20, 2019

### [Techniques for a Newspaper Layout with CSS Grid and Border Lines Between Elements](https://css-tricks.com/techniques-for-a-newspaper-layout-with-css-grid-and-border-lines-between-elements/)

**Article** on Jan 27, 2021

### [The Holy Grail Layout with CSS Grid](https://css-tricks.com/the-holy-grail-layout-with-css-grid/)

**Article** on Dec 10, 2021

### [Using Position Sticky With CSS Grid](https://css-tricks.com/using-position-sticky-with-css-grid/)

### Learning CSS Grid

**Article** on Jul 28, 2017

### [A Collection of Interesting Facts about CSS Grid Layout](https://css-tricks.com/collection-interesting-facts-css-grid-layout/)

**Article** on Aug 17, 2022

### [An Introduction to the \`fr\` CSS unit](https://css-tricks.com/introduction-fr-css-unit/)

**Article** on Sep 22, 2022

### [Auto-Sizing Columns in CSS Grid: \`auto-fill\` vs \`auto-fit\`](https://css-tricks.com/auto-sizing-columns-css-grid-auto-fill-vs-auto-fit/)

**Article** on Aug 2, 2021

### [Centering in CSS: A Complete Guide](https://css-tricks.com/centering-css-complete-guide/)

**Article** on Feb 13, 2018

### [CSS Grid Layout Module Level 2](https://css-tricks.com/css-grid-layout-module-level-2/)

**Article** on Jul 10, 2020

### [CSS Grid in IE: CSS Grid and the New Autoprefixer](https://css-tricks.com/css-grid-in-ie-css-grid-and-the-new-autoprefixer/)

**Article** on Feb 23, 2022

### [Does CSS Grid Replace Flexbox?](https://css-tricks.com/css-grid-replace-flexbox/)

**Article** on Dec 18, 2017

### [Don’t Use My Grid System (or any others)](https://css-tricks.com/dont-use-grid-system-others/)

**Article** on Aug 8, 2022

### [Exploring CSS Grid’s Implicit Grid and Auto-Placement Powers](https://css-tricks.com/exploring-css-grids-implicit-grid-and-auto-placement-powers/)

**Article** on May 20, 2021

### [fit-content and fit-content()](https://css-tricks.com/fit-content-and-fit-content/)

**Article** on Mar 12, 2018

### [Getting Started with CSS Grid](https://css-tricks.com/getting-started-css-grid/)

**Article** on Nov 6, 2017

### [Flexbox and Grids, your layout’s best friends](https://css-tricks.com/flexbox-grids-layouts-best-friends/)

**Article** on May 8, 2018

### [Grid Level 2 and Subgrid](https://css-tricks.com/grid-level-2-and-subgrid/)

**Article** on Jun 25, 2020

### [Grid for layout, flexbox for components](https://css-tricks.com/grid-for-layout-flexbox-for-components/)

**Article** on Jun 20, 2019

### [Hello Subgrid!](https://css-tricks.com/hello-subgrid/)

**Article** on Feb 11, 2021

### [Is CSS float deprecated?](https://css-tricks.com/is-css-float-deprecated/)

**Article** on Feb 14, 2019

### [Quick! What’s the Difference Between Flexbox and Grid?](https://css-tricks.com/quick-whats-the-difference-between-flexbox-and-grid/)

**Article** on Nov 14, 2019

### [Some CSS Grid Strategies for Matching Design Mockups](https://css-tricks.com/some-css-grid-strategies-for-matching-design-mockups/)

**Article** on Jan 7, 2021

### [The Auto-Flowing Powers of Grid’s Dense Keyword](https://css-tricks.com/the-auto-flowing-powers-of-grids-dense-keyword/)

**Article** on Mar 5, 2021

### [The Difference Between Explicit and Implicit Grids](https://css-tricks.com/difference-explicit-implicit-grids/)

**Article** on Mar 20, 2019

### [Things I’ve Learned About CSS Grid Layout](https://css-tricks.com/things-ive-learned-css-grid-layout/)

**Article** on Nov 18, 2020

### [Thinking Outside the Box with CSS Grid](https://css-tricks.com/thinking-outside-the-box-with-css-grid/)

**Article** on Aug 20, 2020

### [To grid or not to grid](https://css-tricks.com/to-grid-or-not-to-grid/)

**Article** on Aug 26, 2022

### [Using Grid Named Areas to Visualize (and Reference) Your Layout](https://css-tricks.com/using-grid-named-areas-to-visualize-and-reference-your-layout/)

**Article** on Jan 11, 2019

### [Why we need CSS subgrid](https://css-tricks.com/why-we-need-css-subgrid/)

### CSS Grid videos

### More CSS Grid sources

-   [CSS Grid Layout Module Level 1](https://www.w3.org/TR/css-grid-1/) (W3C)
-   [Grid by Example](https://gridbyexample.com/) (Rachel Andrew)
-   [Learning CSS Grid Layout](https://rachelandrew.co.uk/archives/2017/03/03/learning-css-grid-layout/) (Rachel Andrew)
-   [CSS Grid Layout: Introduction](https://www.digitalocean.com/community/tutorials/css-css-grid-layout-intro?utm_medium=content_acq&utm_source=css-tricks&utm_campaign=&utm_content=awareness_bestsellers) (DigitalOcean)
-   [CSS Grid Layout: The Repeat Notation](https://www.digitalocean.com/community/tutorials/css-css-grid-layout-repeat-notation?utm_medium=content_acq&utm_source=css-tricks&utm_campaign=&utm_content=awareness_bestsellers) (DigitalOcean)
-   [CSS Grid Layout: The Span Keyword](https://www.digitalocean.com/community/tutorials/css-css-grid-layout-span-keyword?utm_medium=content_acq&utm_source=css-tricks&utm_campaign=&utm_content=awareness_bestsellers) (DigitalOcean)
-   [CSS Grid Layout: The Fr Unit](https://www.digitalocean.com/community/tutorials/css-css-grid-layout-fr-unit?utm_medium=content_acq&utm_source=css-tricks&utm_campaign=&utm_content=awareness_bestsellers) (DigitalOcean)
-   [CSS Grid: Holy Grail Layout](https://www.digitalocean.com/community/tutorials/css-css-grid-holy-grail-layout?utm_medium=content_acq&utm_source=css-tricks&utm_campaign=&utm_content=awareness_bestsellers) (DigitalOcean)
-   [How To Use CSS Grid Properties to Justify and Align Content and Items](https://www.digitalocean.com/community/tutorials/css-align-justify?utm_medium=content_acq&utm_source=css-tricks&utm_campaign=&utm_content=awareness_bestsellers) (DigitalOcean)