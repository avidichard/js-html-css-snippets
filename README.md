# js-html-css-snippets
I decided to start this repository for people to get some easy access to usefull easy code snippets for their website building. All of it free for both personal and commercial, no attribution required (although appreciated). If you found any of this content usefull, please consider clicking the __&#x2606;__ at the top of this page as a sign of appreciation.

## Note on my coding style
You might see that my coding style is less on the conventional side. My background is in the Visual Basic 6.0 department. This means I have been used to use the "Option Explicit" tag forcing me to declare variable names before using them. You won't see me use undeclared variables in the middle of my code. Since my codes are free to use, you may modify, share and redistribute these as you see fit. If you do not like my coding conventions, just change it to how you feel right for you. My goal is to help people find good tools for their website building.

## Table of Contents
__JavaScript__
- [Build Array from String](#js-build-array-from-string)
- [Change an Element's Class by cycling through a Defined Class List](#js-cycle-through-classes)
- [Execute Functions from String Format](#js-execute-functions-from-string-format)
- [Execute Function by String](#js-execute-function-by-string)
- [Intelligent Random Number Generator](#js-intelligent-random-number-generator)
- [Scroll Page to Location](#js-scroll-page-to-location)
- [Simple Open URL](#js-simple-open-url)

## Code Snippets List

### [JS] Build Array from String

```javascript
// Creates an array from a string keeping strings, booleans and numbers as they are
// Example: 'John',4,true,'Smith'
//					will return array:
//						[0] String(John)
//						[1] Number(4)
//						[2] Boolean(true)
//						[3] String(Smith)
// Particularly usefull when wanting to create an arguments array from a string
// Strings MUST be enclosed in single or double quotes which will be removed
// Returns array. NOTE: returns empty array if string is empty. It WILL NOT return
// an array with an empty string at index 0.
function ArrayBuild(s_string, split_str = ",") {
	var retval = [];
	var atmp = s_string.split(split_str);
	var ictr = 0;
	
	if (s_string != "") {
		for (ictr = 0; ictr < atmp.length; ictr ++) {
			// Detect strings by single or double quotes
			if ((atmp[ictr].charAt(0) == "'" && atmp[ictr].charAt(atmp[ictr].length-1) == "'") || 
			(atmp[ictr].charAt(0) == "\"" && atmp[ictr].charAt(atmp[ictr].length-1) == "\"")) {
				// Add a string
				retval.push(atmp[ictr].substr(1,atmp[ictr].length - 2));
			} else {
				// Detects boolean and number values
				switch (atmp[ictr].toLowerCase()) {
					case "true": retval.push(true); break;						// Add true boolean
					case "false": retval.push(false); break;					// Add false boolean
					default: retval.push(Number(atmp[ictr])); break;	// Add Number
				}
			}
		}
	}
	
	return retval;
}
```

### [JS] Cycle Through Classes

```javascript
// Changes an element's class with the next one in line
//          oevent: Just write "event" without quotes, that's it. For stability.
//            elem: The object to cycle the classes or one of the following 2 strings.
//                  You can also use a string in the following formats to define other elements:
//                  -  "tag:TagName": This will automatically detect which element called this function and choose the next element with this tag as the one to manipulate.
//                      Ex: <div onclick="cycleClass('tag:p', 'cls1 cls2')"> <p id="p1" class="cls1"></p> <p id="p2" class="cls1"></p> </div>
//                          This will switch the first p element from cls1 to cls2. It will NOT affect the others, just the first one encountered no matter if it's a direct child or nested.
//                  - "id:ElementID": This will manipulate the classes of the document's element with ElementID
//                  - Anything else will immediately exit the function (even an empty string).
//        sClasses: space separated list of classes to cycle through. It will detect which class is currently set and move on to the next.
//           bLoop: Default (true), loops back to first class from sClasses when last one is met. If set to false, bRemoveLast is taken into consideration.
//     bRemoveLast: Default (false), if bLoop is set to false, this decides if the class should be removed from the element's class list (true) or kept (false)
function cycleClass (oevent, elem, sClasses, bLoop = true, bRemoveLast = false) {
	// Makes sure a valid element was passed
	if (elem === null) {return;}
	// If no classes is defined, this function is useless
	if (sClasses == "") {return;}
	// If there is just 1 class in sClasses and no removal is required then exit
	if (sClasses.indexOf(" ") < 0 && !bRemoveLast) {return;}
	
	var oelem = elem;
	var acls = sClasses.split(" ");		// List of classes
	var ictr = 0;											// Loop counter
	var inext = -1;										// Array index of next class to add
	var astr = [];
	
	if (typeof elem === "string") {
		astr = elem.split(":");
		switch (astr[0].toLowerCase()) {
			case "tag": oelem = oevent.currentTarget.querySelector(astr[1]);break;
			case "id": oelem = document.getElementById(astr[1]);break;
			default: return;
		}
	}
	
	// Loop for all class names starting from the last one
	for (ictr = acls.length -1; ictr >= 0; ictr --) {
		if (oelem.classList.contains(acls[ictr])) {
			// Set next class to add
			inext = ictr + 1;
			if (inext > acls.length - 1) {inext = 0;}
			// Remove the current class
			if (bRemoveLast || bLoop) {oelem.classList.remove(acls[ictr]);}
			// Exit if no loop is required
			if (inext == 0 && !bLoop) {return;}
			// Add the next class and exit
			oelem.classList.add(acls[inext]);
			return;
		}
	}
	
}
```

### [JS] Execute Functions from String Format

__REQUIRED FUNCTIONS:__

- [Build Array from String](#js-build-array-from-string)
- [Execute Function by String](#js-execute-function-by-string)

```javascript
// Executes functions safely from a string
// Rules: functions separated by ";; ". Parameters separated by ",,".
// Format: FunctionName,, Param1,, Param2;; FunctionName,, Param1,, etc...
function ExecuteFunctions(FuncAndParams) {
	var afuncs = [];			// List of functions
	var aparams = [];			// List of function parameters
	var ictr = 0;					// Function list looper
	var ipos = 0;					// Parameter seperator position
	var sparams = "";			// Function parameters string to be put as an array in aparams
	var sfunc = "";				// Current function name in the loop
	
	afuncs = FuncAndParams.split(";; ");
	
	for (ictr = 0; ictr < afuncs.length; ictr ++) {
		sparams = "";
		// Check if there are any parameters
		ipos = afuncs[ictr].indexOf(",, ");
		if (ipos > 0) {
			sfunc = afuncs[ictr].substr(0, ipos);		// Get function name
			sparams = afuncs[ictr].substr(ipos+3);	// Get parameters
		} else {sfunc = afuncs[ictr];}	// If no parameters, use current function string as function name
		aparams = ArrayBuild(sparams,",, ");			// Build parameters array
		ExecFunctionByString(sfunc,...aparams);		// Execute function
	}
	
}
```

### [JS] Execute Function by String

```javascript
// Executes a function using it's string name
function ExecFunctionByString(funcName, ...funcParams) {
	var ofunc = window[funcName];
	
	if ((typeof ofunc).toLowerCase() === "function") {
		ofunc.apply(undefined,funcParams);
	}
}
```

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
