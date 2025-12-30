**There are two property that controls most of the CSS Typography**. They are font and text
## Font
###### ==font family==
`monospace cursive sans-seriff seriff`
All `seriff` font character have stroke ending. It is opposite for `sans-seriff`. `monospace` just means all the character have same width. There are other also. Font name that have space character should always be enclosed in double quotes.
always have fallback font family
###### ==font-style== 
`normal italic oblique`. `oblique` means being more slanted that italic.
###### ==font-weight== :
`100 ... 900`
###### ==font-variant==
`small small-caps`. `small-caps` means in capitalized letters in small size.
###### ==font-size==
`16px 1rem 1em 100% 1vw` . write `font-size: 100%` in `body` so every browser can have same size. When percentage is used, it is related to parent size.
###### ==font==
all collection of upper properties
## Text
#### color
let's understand the color. Always use hex value or hsl or other value. Don't use colors like `red` or whatever as different browser use different value for that text(`red`).
#### Text Alignment and Text Direction 
###### ==text-align==
`right` `left`, `center`, `justify`. 
`justify` stretches the lines so that each line has equal width, like in newspaper. only works in inline
###### ==text-align-last== 
`right`, `left`, `center`, `justify`.
It helps align the last line of the paragraph.
###### ==direction==
`rtl`, `ltr`. Specifies the text direction/writing direction. 
###### ==vertical-align==
`25px, -25px, 50%, 1cm`. 
Can take any other value but `px` is recommended. It is used to have text which doesn't follows the normal flow. It we want to have text little bit above or below the normal flow. Other values are there but not necessary.
###### ==unicode-bidi==
`normal | embed | bidi-override`
It is used to make words in normal or reverse order. meaning that text -> text. `bidi-override` is used to do such. 

#### Text Decoration
###### ==text-decoration-line== 
`underline`, `overline`, `line-through`. It can have more than one value. like `underline overline` is valid
###### ==text-decoration-color==
###### ==text-decoration-style==
`solid(default)`, `dashed`, `double`, `dotted`, `wavy`, etc.
###### ==text-decoration-thickness==
`5px, 35%, auto`
###### ==text-decoration==
`combined properties`
#### text-transform
###### ==text-transform==
`uppercase lowercase capitalize none`. Here `none` make it such that text appear as the in HTML code.

#### Text-Spacing
###### ==text-indent==
`4px 5em 34% -3em`. It is used to indent the first line. It is used to whole selector. There is not need to create extra selector like `span` or any other. negative values are also allowed.
###### ==letter-spacing== 
`2px`. used to specify the space between the character,
##### ==line-height==
`1(default) 1.5(recommended)`. used to specify the space between line. unit is related to font-size. By default if no unit is provided, it is related to font size of that element.
###### ==word-spacing==
`10px -2px`. used to specify the space between words
###### ==white-spacing==
`wrap nowrap normal`. It is used to specify whether how to handle white-space in the element. like `nowrap` means it doesn't wrap at all and no matter what the text doesn't goes to another line which implies that white-space in between text is not wrapped.
###### ==text-shadow==
`x-offset y-offset blur-effect color`. It can have more than one value     `1px 1px 1px red, 4px 4px 4px violet` is completely valid
###### ==text-overflow==
`clip ellipsis`: However, `white-space` and `overflow` is required for this property to apply. here `clip` means that the overflowed content willn't be visible and `ellipsis` means that three dot will appear.
###### ==word-wrap==
`normal break-all keep-all`
###### ==writing-mode==

![[Writing-Mode.png]]

