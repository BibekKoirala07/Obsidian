**Both 2D and 3D Transform**

#### Transform
- `transform` : `translate(x, y, z)`

#### Rotate
- `transform`: `rotate(20deg)`

#### Scale
- `transfrom`: `scale(2,3)`
#### Skew
- `transform`: `skew(degree)`

### Transition
- `transition-delay` : `1s`

- `transition-duration` : `2s`

- `transition-property` : `width transform` or any other property

- `transition-timing-function` : 
 - `ease` : slow start, then fast and end slowly
 - `linear` : same speed everywhere
 - `ease-in` : slow start
 - `ease-out` : slow end
 - `ease-in-out` : slow start and slow end

- `transition` : shorthand

Example : 
```
transition: width 2s, height 2s
```

## Animations
- `animation-delay`

- `animation-duration`

- `animation-name`

- `animation-duration`: 

- `animation-direction` : 
  - `normal` : played as normal
  - `reverse` : 
  - `alternate` : forward first then backwards
  - `alternate-reverse` : backward first and forward

- `animation-iteration-count`: `4 infinte` How many times an animation should run

- `animation-timing-function` : same as `transition-timing-function`

- `animation-fill-mode` :
 - `none` : styles will not be applied after the animation is finished
 - `forwards` : will retain the last style value
 - `backwards` : will retain the first style value
 - `both` : will follor the both fowards and backwards

- `animation` : shorthand
### Animation Cold
```
@keyframes example {
	0% { background-color: red}
	25% {}
	50% {}
	75% {}
	100% {}
}

div {
	animation-name: example
	animation-duration: 4s
}
```


