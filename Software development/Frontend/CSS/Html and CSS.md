[[CSS Cheatsheet.jpg]]
## Height/Width
units: `auto length %`
- they don't include `padding`, `border`, and `margin`. All these three are extra and should be added to get full-width but it can be bypassed if we apply property of `border-box`: `box-sizing`
- normally, have `max-with` or `max-height`. If we use both `width` and `max-width` and value of `width` > `max-width`, the value of `width` is completely ignored
## Margin
- `margin: top right bottom left` : `auto` is used to stack element to one side
- `auto` is used in flex-children to control them also. It is convenient to do so.
- It is inheritable. It may not be used that much
- margin-collapse: many times. the collective margin ends up being just biggest margin of single tag, which can be frustuating.
## Padding
- same as margin
## Outline
- not a part of element and doesn't take space so, it may overlap other element 
- `outline-style` : `required` units : same as border.    no other property works if we don't specify this. means `outline-width` doesn't work if we don't `outline-style` isn't set
- `outline-width` : same as border 
- `outline-color` : extra value `invert`. to make outline visible
- `outline-offset`: the space between `outline` and `border`
## [[Typography]]
## [[Layout]]

