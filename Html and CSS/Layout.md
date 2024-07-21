## Block Element and Inline
- Block-level : div, p , h1-h6, form, header, footer, sector
- Inline-level : span, a, img, strong
## Display
- `inline`, we can't set the `width/height` of an element , they automatically adjust to the content and it doesn't respect `margin-top` and `margin-bottom`
- `inline-block`, it doesn't add a line-break after a element, so element can sit next to other element if there is space. It can be sometimes used to replace flex too
-  `block`, 
- `none` element will be hidden and pages will be displayed as if the element is not there while [[visiblity]] : `hidden` only make it invisible but element will take up the same space.
## Position
- `position` : `static(default) relative fixed absolut sticky`. element is positioned using `top,` ` bottom`, `left`, `right`. but we can also use `inset: top right bottom left`. position `fixed` means the element stays in the same place even if page is scrolled. position `sticky` is based on user's scroll position. for `top: 0`, the element stick at the top of the page when we reach a scroll position
## Z-Index
- `z-index:` `1 2 -1 -5`
- it only works on positioned element and flex children(items)
- if we don't specify the z-index in positioned elements then, the last html element will be shown on top
## Overflow
- `overflow`: `visible(default) hidden scroll auto`. 
- only works on block element with specified height
- we have `overflow-x` and `overflow-y`
- `overflow-wrap` : `break-word normal`. used to specify whether we should break word if the word overflow the container


## Float and Clear
- `float`: `left right none(default)`