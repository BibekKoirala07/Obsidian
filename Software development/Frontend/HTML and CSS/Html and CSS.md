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
### Accessibilities
Accessibility (a11y) ensures **people with disabilities** can use your website — especially using screen readers, keyboard navigation, etc.
Accessible Rich Internet application. It is the way to explain the content to the person who can't read the screen for some reasons.
When we use standard Html element like `button`, `a` or other things, it is already accessible. but what about the custom dropdown that we may have made ?  using `div`. They aren't accessible. so what to do ?. Assistive technologies will not understand that right ?
There are three kinds of aria labels


> [!NOTE] Caution
> . Don't put it in native HTML semantic element.

###### Aria Roles
```html
	<div roles="button"></div> // it means you are using this div as button
	<div roles="navigation"></div> // it means you are using this div as navbar
```
###### Aria State
This describe the current condition of element. That means it often changes based on user interaction. Use js to ensure that aria are also updated since you are building custom interactive component then you have to use js anyway. Do that in js. Like you may make custom checkbox or custom accordion.
```html
	<div aria-expanded="true or false"></div> whether an element is open or collapsed
	<div aria-pressed="true or false"></div> whether an element pressed
	<div aria-selected></div> whether an element is selected
	<div aria-disabled></div> whether a element is disabled when not using                                    button disabled
```
###### Aria Properties
It gives extra information's about the element's relationships or labels.
```html
	<div aria-labelledBy=""></div> // referencing another element as label
	<div aria-descirbedBy=""></div> // linking to a description
	<div aria-contorls=""></div> / telling what element this controls
```


### Attributes
###### Global Attributes
``` html
<div
  id="box"                         unique identifier
  class="red"                      CSS class name
  style="color: red;"              inline CSS style
  title="I am a box"               tooltip text on hover
  role="button"                    ARIA role for accessibility
  
  tabindex="0"                     keyboard navigation order
  
  data-*="123"              custom data attribute for JS browser still ignores it but it is semantically correct that is difference with random attributes
  
  aria-pressed="false"            accessibility state (pressed or not)
  
  hidden                          hides the element from layout and screen readers
  
  contenteditable                 allows user to edit text inside
  
  lang="en"                       language of content
  
  onclick="alert('clicked')"      runs JS when clicked

  // random element
  random="cheese" // the things is we can add random things and rendering process willnot break or anything. in fact we can even read this value from js.
  however donot do this. If you really need it, do it in date-*
></div>
```

### Head Tags
Link tag in Head defines the link between current HTML webpage and external sources. It always goes inside the head. it is like saying `hey browser, there is the external file that I want you to load and associate with this page `

```html
<link
	href="" path
	rel="" What kinds of link it is
	type="" media type like MIME like text/css or image/x-icon
	media="" target media (like screen, print and all)
	crossOrigin="" Required if the resources is from other domain
/>
```
However link isn't for js. You have to use `script` for that. It is the exception and it doesn't product anything in screen at all. It just assists.
###### Basics
This tell website about the title, character encoding and making page responsive.
```html
<html lang="en">
	<head>
		<title>My Portfolio</title>
		<meta charset="UTF-8"> {/* It set up character encoding which also supports emoji */} //  it ensures foreign language and emojis
		<meta name="viewport" content="width=device-width, initial-scale=1.0">
	</head>
</html>
```
###### Web Icons
```html
<html>
	<head>
		<link rel="icon" href="favicon.ico"> // favicon
		<link rel="apple-touch-icon" href="icon.png"> // apple icon
		<link rel="manifest" href="/site.webmanifest"> // PWA icon
	</head>
</html>
```
###### Styling and font
###### JS And behavior
It loads and run JS code. It is not put in head why ? cuz when the script is loading, the browser pauses HTML parsing which cause delay in rendering the page. However if we use `defer` or `async` then 
```html
<html>
	<head>
		
	</head>
</html>
```
###### preload, pre-fetch, preconnected and dns-prefetch
We use this in link to make our website feel faster. It tells browser how to load certain resources even before they are needed. It is critical for font, js, css, Hero Images etc. 
```html
<link rel="preload" href="/fonts/Roboto.woff2" as="font" type="font/woff2" crossorigin="anonymous" />
<link rel="preload" href="/main.css" as="style" />
```
Here preload means telling browser to load the file early. as means what kinds of resources is it.
```html
<link rel="prefetch" href="/next-page.html" />
<link rel="prefetch" href="/js/chart.js" as="script" />
```
`Hey browser, when everything is done go to the source and get this file quietly` Like it may be necessary for the JS file that we may need later and for image that may be used. It is meant for future navigation and actions.

```html
<link rel="dns-prefetch" href="//fonts.googleapis.com">
```
It is also same as `dns-prefetch`. Notice you don't type `https`. It means we are just resolving the domain so that we get ready when we need third party resources. It doesn't connect but just resolves domain that is it. Like we may need it for fonts, CDN or analytics.

```html
<link rel="preconnect" href="https://fonts.googleapis.com" crossorigin>
```
Just like `dns-prefetch` but it takes one step further. It not only resolves DNS domain but also setup full connection. which is `DNS + TCP handshake + TLS negotiation`. So, when you need something, connection is already established. `crossOrigin` is necessary if resources is fetch from other domain(mostly CORS/CDN).

Here `preconnect` generally means telling the browser that you may need to connect to this domain so go ahead and get ready now. It saves you times when you need to load something from other domain.

###### Social Media
Facebook uses Open Graph and Twitter uses Twitter Cards. Social Media when shared just doesn't just share link. It tries to fetch a preview. Without them , Social Media just guess. This helps when users share your page. Always share high quality image.
```html
<html lang="en">
	<head>
		<!-- Facebook/LinkedIn/Discord -->
		<meta property="og:title" content="Learn HTML the Right Way">
		<meta property="og:description" content="An easy HTML tutorial.">
		<meta property="og:image" content="https://example.com/image.jpg">(full Url)
		<meta property="og:url" content="https://example.com/html-guide">(Canonical URL)
		
		<!-- Twitter -->
		<meta name="twitter:card" content="summary_large_image" />
		<meta name="twitter:title" content="Bibek's Blog" /> 
		<meta name="twitter:description" content="Learn frontend fundamentals clearly." />
		<meta name="twitter:image" content="https://example.com/images/cover.jpg" />

	</head>
</html>
```
The goal is to help search engines to know what the page is about. We should be careful while writing `head` tag in html. It denotes how browsers, search engines and social platorm understand your page better. `head` tag isn't shown directly in website.
```html
<html lang="en">
	<head>
		<title>My Portfolio</title>
		<meta charset="UTF-8"> {/* It set up character encoding which also supports emoji */}
		<meta name="viewport" content="width=device-width, initial-scale=1.0">

		// short summary shown in search result
		<meta name="description" content="Learn HTML and Web Fundamentals.">
		// Old-school keywords (mostly ignored by Google now)
		<meta name="keywords" content="HTML, web, frontend, basics">
		// Tells search engines whether to index the page
		<meta name="robots" content="index, follow">

		<!-- Facebook/LinkedIn/Discord -->
		<meta property="og:title" content="Learn HTML the Right Way">
		<meta property="og:description" content="An easy HTML tutorial.">
		<meta property="og:image" content="https://example.com/image.jpg">(full Url)
		<meta property="og:url" content="https://example.com/html-guide">(Canonical URL)
		
		<!-- Twitter -->
		<meta name="twitter:card" content="summary_large_image" />
		<meta name="twitter:title" content="Bibek's Blog" /> 
		<meta name="twitter:description" content="Learn frontend fundamentals clearly." />
		<meta name="twitter:image" content="https://example.com/images/cover.jpg" />

	</head>
</html>
```


### Local and Session Storage
Normally we would think that they aren't part of html. But they are part of HTML5(they refers to the new HTML markup element and JS API (for function)). However JS is need to work with those API even though they are part of HTML5.
### HTML document Structure
### Event Bubbling and Event Delegation and Event Propagation and Event Capturing

### Web Security

