**There are two property that controls most of the CSS Typography**
1. Font
2. Text 
## Font
1. `font-family`: always have fallback font family
2. `font-style` : `normal italic oblique`
3. `font-weight` : `100 ... 900`
4. `font-variant` : `small small-caps`
5. `font-size` : `16px 1rem 1em 100% 1vw` . write `font-size: 100%` in `body` so every browser can have same size.
6. `font`: all collection of upper properties
## Text
###### `color`
###### Text Alignment and Text Direction 
1. 1. `text-align` : `right` `left`, `center`, `justify`. `justify` stretches the lines so that each line has equal width, like in newspaper. only works in inline
2. 2. `text-align-last` : `right`, `left`, `center`, `justify`. It helps align the last line of the paragraph.
3. 3. `direction` : `rtl`, `ltr`. Specifies the text direction/writing direction. 
4. 4. `vertical-align` : `25px, -25px, 50%, 1cm`. Can take any other value but `px` is recommended. It is used to have text which doesn't follows the normal flow. It we want to have text little bit above or below the normal flow. Other values are there but not necessary.
5. 5. `unicode-bidi` : `normal|embed|bidi-override`. It is used to make words in normal or reverse order. meaning that text -> text. `bidi-override` is used to do such. 

6. Text Decoration
7. . `text-decoration-line` : `underline`, `overline`, `line-through`. It can have more than one value. like `underline overline` is valid
8. . `text-decoration-color` : `any-color`.
9. . `text-decoration-style` : `solid(default)`, `dashed`, `double`, `dotted`, `wavy`, etc.
10. . `text-decoration-thickness` : `5px, 35%, auto`,
11. . `text-decoration` : combined properties

12. `text-transform` : `uppercase`, `lowercase`, `capitalize`

13.  Text-Spacing
14. . `text-indent`: `4px 5em 34%`. It is used to indent the first line. It is used to whole selector. There is not need to create extra selector like `span` or any other. 
15. . `letter-spacing` : `2px`. used to specify the space between the character,
16. . `line-height` : `1(default) 1.5(recommended)`. used to specify the space between line. unit is related to font-size
17. . `word-spacing` :  `10px -2px`. used to specify the space between words
18. . `white-spacing` : `wrap nowrap normal`. It is used to specify whether how to handle white-space in the element. like `nowrap` means it doesn't wrap at all and no matter what the text doesn't goes to another line which implies that white-space in between text is not wrapped.
19. . `text-shadow` : `x-offset y-offset blue-effect color`. It can have more than one value     `1px 1px 1px red, 4px 4px 4px violet` is completely valid
20. . `text-overflow`: `clip ellipsis`: However, `white-space` and `overflow` is required for this property to apply.
21. . `word-wrap`: `normal break-all keep-all`. 
22. . `writing-mode`: ![[Writing-Mode.png]]

