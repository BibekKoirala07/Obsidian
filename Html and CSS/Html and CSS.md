## [[CSS Syntax]]
### CSS Syntax

```
h1 {
	color: 23px;
}
```
`h1` = selector
`color` = property
`23px` = value


## [[Units]] // not completed
## [[Selector]]
 // not completed
## Colors
- RGB format - Red, Green, Blue
- HEX format
- HSL format
- RGBA format - same with RBG but with A as opacity
- HSLA format - Same with HSL but with A as opacity

## [[Background]]

## Borders
- `border-style`: `dotted dashed soldi double groove inset outset none hidden` (`compulsory`)
- `border-width`: `top right bottom left`
- `border-color`: `top right bottom left`
- `border`: shorthand
- can be set for every side and use `inline` and `block more`
- `border-radius`: `any length`
#### Border Image

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
## Links
- we can have pseudo class here. they are `:link`, `:visited`, `:hover`, `:active`. These property should be in given order and not anything else. Otherwise, it won't work


## Cursors
- `cursor`: `auto crosshair pointer progress`


## List
- `list-style-type` : `` circle square upper-roman lower-alpha`
- `list-style-image`: `url(image)`
- `list-style-position`: `outside`, means bullet will be outside the list item, `inside`,
- `list-style`: shorthand


## [[Table]]  //not completed
## [[Layout]]
## [[Forms]]
## [[Hierarchy]]
## Math Function
- calc - `calc(100% - 100px)`
- max - `max(value1, value2, value3)`
- min - `min(value1, value2, value3)`


## Box-Shadow
- `box-shadow`: `horizontal-offset vertical-offset blur spread-radius color inset(optional, it denotes that shadow will be inward)`. multiple shadow can be added

## [[Animations]]
## Filters
- `filter` : `grayscale(%)` // changes the whole picture to black and white
- `filter` : `blue(px)` // apply blue
- `filter` : `brightness(%)` // adjust brightness 0->black, 100-> original image
- `filter` : `constrast(%)` same as brightness
- `filter` : `opacity(0 - 1)`
- 


## Masking
## Column
## Variables
## [[Media Queries]]
## [[Flexbox]]
## [[Grid]]

