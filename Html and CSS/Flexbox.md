![[Flexbox.png]]
- `display` : `flex || inline-flex` // have no effect on container
- `flex-direction` : `row column row-reverse column-reverse`
- `flex-wrap` : `wrap nowrap wrap-reverse`
- `flex-flow` : `row wrap`
- `justify-content` : `center space-between space-evenly space-around end start flex-start(involves writing mode) flex-end(involves writing mode)`
- `align-items` : `center flex-start flex-end stretch baseline`
- `align-content` :  same as `align-items` //similar to justify content but with cross-axis
- `gap`: `row column`
### Flex-Children
- `order` : `(0) 1 2 3`
- `flex-grow`: `0(default)` //item to grow if necessary
- `flex-shrink` : `1(default)`
- `flex-basis` : `(auto) 200px` // specifies the initial width or height of item depending on flex-direction. flex-direction: column -> height. If the height and width is already set, It takes the value from those properties. Lets say we have `auto`, then have `height` and `width` set, that means flex-basis is set from those value. `max-height max-width` , `min-height-width`always overwrite` flex-basis`. as `flex-basis` isn't final
- `align-self` : same as `align-item` but for individual item
- `flex: grow shrink basis`, 
- **Tips** : with `flex: 1`, we get same width children. How? well we are setting `grow: 1` and `basis` to `0`. As, basis is 0, it shrinks as much it can but `grow` again resize it.


### Basics and terminology







#### justify-content

![flex items within a flex container demonstrating the different spacing options](https://css-tricks.com/wp-content/uploads/2018/10/justify-content.svg)

  



#### align-items

![demonstration of differnet alignment options, like all boxes stuck to the top of a flex parent, the bottom, stretched out, or along a baseline](https://css-tricks.com/wp-content/uploads/2018/10/align-items.svg)


#### align-content

![examples of the align-content property where a group of items cluster at the top or bottom, or stretch out to fill the space, or have spacing.](https://css-tricks.com/wp-content/uploads/2018/10/align-content.svg)


#### gap, row-gap, column-gap

![](https://css-tricks.com/wp-content/uploads/2021/09/gap-1.svg)


![](https://css-tricks.com/wp-content/uploads/2018/10/02-items.svg)

## Properties for the Children  
(flex items)

#### order

![Diagram showing flexbox order. A container with the items being 1 1 1 2 3, -1 1 2 5, and 2 2 99.](https://css-tricks.com/wp-content/uploads/2018/10/order.svg)

  
By default, flex items are laid out in the source order. However, the `order` property controls the order in which they appear in the flex container.

```
.item {
  order: 5; /* default is 0 */
}
```

Items with the same `order` revert to source order.

#### flex-grow

![two rows of items, the first has all equally-sized items with equal flex-grow numbers, the second with the center item at twice the width because its value is 2 instead of 1.](https://css-tricks.com/wp-content/uploads/2018/10/flex-grow.svg)

  
This defines the ability for a flex item to grow if necessary. It accepts a unitless value that serves as a proportion. It dictates what amount of the available space inside the flex container the item should take up.

If all items have `flex-grow` set to `1`, the remaining space in the container will be distributed equally to all children. If one of the children has a value of `2`, that child would take up twice as much of the space either one of the others (or it will try, at least).

```
.item {
  flex-grow: 4; /* default 0 */
}
```

Negative numbers are invalid.

#### flex-shrink

This defines the ability for a flex item to shrink if necessary.

```
.item {
  flex-shrink: 3; /* default 1 */
}
```

Negative numbers are invalid.

#### flex-basis

This defines the default size of an element before the remaining space is distributed. It can be a length (e.g. 20%, 5rem, etc.) or a keyword. The `auto` keyword means “look at my width or height property” (which was temporarily done by the `main-size` keyword until deprecated). The `content` keyword means “size it based on the item’s content” – this keyword isn’t well supported yet, so it’s hard to test and harder to know what its brethren `max-content`, `min-content`, and `fit-content` do.

```
.item {
  flex-basis:  | auto; /* default auto */
}
```

If set to `0`, the extra space around content isn’t factored in. If set to `auto`, the extra space is distributed based on its `flex-grow` value. [See this graphic.](http://www.w3.org/TR/css3-flexbox/images/rel-vs-abs-flex.svg)

#### flex

This is the shorthand for `flex-grow,` `flex-shrink` and `flex-basis` combined. The second and third parameters (`flex-shrink` and `flex-basis`) are optional. The default is `0 1 auto`, but if you set it with a single number value, like `flex: 5;`, that changes the `flex-basis` to 0%, so it’s like setting `flex-grow: 5; flex-shrink: 1; flex-basis: 0%;`.

```
.item {
  flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]
}
```

**It is recommended that you use this shorthand property** rather than set the individual properties. The shorthand sets the other values intelligently.

#### align-self

![One item with a align-self value is positioned along the bottom of a flex parent instead of the top where all the rest of the items are.](https://css-tricks.com/wp-content/uploads/2018/10/align-self.svg)
  
This allows the default alignment (or the one specified by `align-items`) to be overridden for individual flex items.

Please see the `align-items` explanation to understand the available values.

```
.item {
  align-self: auto | flex-start | flex-end | center | baseline | stretch;
}
```

Note that `float`, `clear` and `vertical-align` have no effect on a flex item.
### Flexbox tricks!

### Browser support

#### Desktop

| Chrome | Firefox | IE | Edge | Safari |
| --- | --- | --- | --- | --- |
| 21\* | 28 | 11 | 12 | 6.1\* |

#### Mobile / Tablet

| Android Chrome | Android Firefox | Android | iOS Safari |
| --- | --- | --- | --- |
| 117 | 118 | 4.4 | 7.0-7.1\* |

### Bugs

Flexbox is certainly not without its bugs. The best collection of them I’ve seen is Philip Walton and Greg Whitworth’s [Flexbugs](https://github.com/philipwalton/flexbugs). It’s an open-source place to track all of them, so I think it’s best to just link to that.


