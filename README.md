# js-html-css-snippets
I decided to start this repository for people to get some easy access to usefull easy code snippets for their website building. All of it free for both personal and commercial, no attribution required.

<ins>__Note on my coding style__</ins>

You might see that my coding style is less on the conventional side. My background is in the Visual Basic 6.0 department. This means I have been used to use the "Option Explicit" tag forcing me to declare variable names before using them. You won't see me use undeclared variables in the middle of my code. Since my codes are free to use, you may modify, share and redistribute these as you see fit. If you do not like my coding conventions, just change it to how you feel right for you. My goal is to help people find good tools for their website building.

## Table of Contents
__JavaScript__
- [Smart Random Number Generator](#js-intelligent-random-number-generator)
- [Scroll Page to Location](#js-scroll-page-to-location)
- [Simple Open URL](#js-simple-open-url)

## Code Snippets List

### [JS] Intelligent Random Number Generator

```javascript
// Intelligent random number generator
// sround: {Empty String} = Do not round (default)
//				 f = Floor (round down)
//				 c = Ceiling (round up)
function randomNum(rangeMin, rangeMax,sround = "") {
	
	var iran = Math.random();
	var iadd = 1;
	var retval = 0;
	
	// If max range is lower than 1, then do not add 1 in the calculations
	if (rangeMax < 1) {iadd = 0;}
	
	switch (sround.toLowerCase()) {
		case "": retval = iran * (rangeMax - rangeMin + iadd) + rangeMin;break;
		case "f": retval = Math.floor(iran * (rangeMax - rangeMin + iadd) + rangeMin);break;
		case "c": retval = Math.ceil(iran * (rangeMax - rangeMin + iadd) + rangeMin);break;
	}
	
	// If generated numbers are lower or greater than the range, make sure that
	// we return the min or max range.
	if (retval < rangeMin) {retval = rangeMin;}
	if (retval > rangeMax) {retval = rangeMax;}
	
	return retval;
	
}
```

### [JS] Scroll Page to Location

```javascript
// Scrolls the page to a specific location OR at a specific element's location
// Default is scroll to top.
// MoveToElem(X or Y): specifies to force page to scroll at the element's X, Y location or not
function PageScroll(pos_x = 0, pos_y = 0, elem_id = "", MoveToElemX = false, MoveToElemY = true) {
	if (elem_id != "") {
		var oelem = document.getElementById(elem_id);
	}
	
	var iposx = pos_x;
	var iposy = pos_y;
	
	// Get element's X and Y positions
	if (elem_id != "") {
		if (MoveToElemX) {iposx = oelem.offsetLeft;}
		if (MoveToElemY) {iposy = oelem.offsetTop;}
	}
	
	window.scrollTo(iposx,iposy);
}
```

### [JS] Simple Open URL

```javascript
// Open a url in the current tab or in a new one
function url_open(s_url, b_newtab = false) {
	
	// If the link is empty, just exit, never mind continuing
	if (s_url == "") {return;}
	
	if (b_newtab) {
		window.open(s_url, "_blank");
		return;
	}
	
	window.location.href = s_url;
	
}
```
