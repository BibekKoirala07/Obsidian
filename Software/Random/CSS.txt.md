-------Tips--------
/use hex value as same color can means different to different browsers
/always declare margin padding to 0 cuz different browsers have different default values
alwasys use variable
make button bigger in mobile as screen is less

color-scheme: dark light day-mode night-mode

-----Scroll-------
scroll-behaviour: auto | smooth //it doesn't work in user scroll behaviour, it only works when some portion is directly pointed
scroll-margin: 
scroll-padding:

----------TYPOGRAPHY---------
--Font--
font-family --@font-face
font-weight: 100 ... 900 | bold | relative
font-style: italic | oblique | normal | oblique .deg    //how much text is slanted
font-variant: normal | small-caps
font-size: 
color:
text-transform : uppercase | lowercase | capitalize
text-align: left | right | center | justify(The text will be stretched, so each line has the same width) --text align only works on inline 

Text-Decoration
 - text-decoration: overline | underline | line-through | none -decoration-color- solid | double | dotted | dashed  | wavy  -decoration-thickness
Text-Spacing
 - text-indent: in px
 - letter-spacing: in px
 - line-height: number and 1 is default and use 1.5 and make it unitless. it does accept other units too but use unitless and best number is 1.5 and it is related to font size
 - word-spacing: in normal unit
 - white-space: wrap | nowrap | pre | pre-line | pre-wrap | inherit
text-shadow : horizontal-shadow vertical-shadow blur-effect color | and it can be repeated multiple times for same text
Overflow: visible | scroll | auto | hidden // only works for block level element with specified height
 --subproperty: overflow-x,-y

----------- LAYOUT ----------
width: % (preferred) | length | min() | max() | minmax() | maxcontent | mincontent | fitcontent  //height of body is combination of all css element
padding:
margin: //when an element touches its parent, its top and bottom margins will merge wit the margins of parent elements. solution is to add padding to parent elements
display : inline | inline-block | block | flex | grid | none
 // inline-block is inline but width and height can be set. inline-block elements support height, width, margin and padding.inline - m-left, p-left and right
 // margin: auto in flex child can help position it better. and use flex-grow and basis
 // grid and flex stretch by default
 // use margin in flex. it is better than others

flex: flex-direction, flex-wrap, align-items, align-content, align-self

grid: 


aspect-ratio: width/height -- to preserve the ratio of width and height in varying environment 
	//is ignored if it has both width and height attribute set
	// it is a weakest way so don't use it

backdropfilter()

Background: color | image url(), can be multiple | repeat | position | attachment | size
 - background-repeat: repeat | repeat-x | repeat-y
 - background-position: x-axis y-axis
 - background-size: length | cover | contain
 - background-clip: border-box | padding-box | content-box // doesn't take other value
opacity is also used!


-----Border------
border: 1px solid | dotted | dashed | double | groove colour
 	-- border-top/bottom/block/inline - width/style/color
 	
border-radius: top and bottom value/left and right value or any normal form

subproperty: border-bottom-left-radius, border-top-right-radius, ..... 

----- Borders in table ------
border-collapse: collapse | separate
--border-spacing: 0px, 1px //use to space borders

position : static | absolute | relative | sticky | fixed 
use top bottom left right
//when we use postion absolute to block level element, then it takes some property of inline element.


Hide and element // display: none or visibility: hidden
none display like element is not there
hidden will still take up a place but will not be visible

Units eg. px, em, rem, vw, vh , dvw, dvh, %, ch, vmin, vmax


min max clamp
maxcontent  width maxcontent downside is that if our viewport shrinks, the element associated with such CSS property will overflow from screen
mincontent width it is about getting smallest as it gets and it looks for longest word in sentence and put background accordingly
fitcontent it takes advantages of both mincontent and maxcontent it is not supported by firefox 

Logical properties
block and inline eg. margin-block

Variables
min()
max()
minmax()
currentcolor ?? it gives color of text to border eg. border: 1px solid currentcolor

Pseudo Element
::root()
::first-line()
::first-letter()
::before()
::after()
::selection() ?? What to do after text is selected ? turn the text to red? blue? eg. p:seletion() 
::marker ?? for styling the list style of list

Pseudo classes

:fullscreen()
:active()
:hover()
:nth-child()
:last-child() eg.p:lastchild() ?? select every p element/tags that is last child of its parent element
:focus()
:has() // can be nested also ??
:is()
:where()
:modal()
:placeholder-shown()

--Input
:disabled()
:enabled()


Subgrids
Insets ?? distance between parent elements and itselfbibek@bibek-Aspire-E5-576G:~/Documents/Cheatsheets$ 
