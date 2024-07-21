## Simple Selector
   **Selecting with Class, tags and ids**
    `p.selected` - selects `p` tags with `selected` class
    `p#selected` - selects `p` tags with `selected` id

## Combinator Selector
 There are 4 kinds of this selector.
 1. descendant selector (space)
 2. General Sibling Selector(~). It only select the tags after its parents. However it select all the sibling element unlike `+`
 3. Direct Child Selector(>)
 4. Adjacent Sibling Selector(+). Used to select the directly another selector right after parent selector. like if we have `div + p`, then it just select the `p` that comes right after `div`. Means another `p` after that first `p` isn't selected. 

## Pseudo-class selectors
incomplete
1.  :`hover`
2. `:fist-child` :  `p:first-child`, it select any p that is first child of parent element
3. Anchor - `:link`, `:visited`, `:hover`, `:active`
4. input - `:focus`, `:disable`

## Pseudo-elements selectors
- `::first-letter` - only to block element. This can also mean inside a `::before` element.
- `::first-line` - only applied to block element.
- `::marker`- it applies to list item or `li`
- `::selection` - it applies to element that is selected by user
- `::before`, `::after` - we use content property to add content
- `::backdrop`: Creates a box that is as large as the user viewport that allows you to style the background of any element that is turned to full-screen, such as videos.
- `::placeholder`
- `::cue` – You can use this to style WebVTT cues, meaning things like subtitles and captions inside videos.


## Attribute Selectors
I have to do it later
