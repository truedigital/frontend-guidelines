#Front-end Guidelines
A collection of front-end practice guidelines, largley a subset of [Front-End Dev guidelines](http://tech.tmw.co.uk/code/TMW-frontend-guidelines/#section-intro) by TMW.

## HTML
###General
* All front-end code should display clear separation of presentation, content, and behaviour.
* Markup should be well formed, semantically correct and generally valid.
* Gracefully degrade functionality when not present (e.g GPS, box-shadow, forms etc).

###Indentation
For all languages, indent your code with tabs. The default tab size should be set as 4.

###Browser Support
Browser support may vary on a per-project basis, but generally:

* Internet Explorer 8+
* Firefox
* Google Chrome (Windows, OSX, iOS and Android)
* Safari (OSX and iOS only)
* Android Browser

We test in the latest versions of Firefox, Chrome and Safari due to them using incremental auto-updates.

###Markup Compatibility and Structure
* Develop to the HTML5 standard and consider the user of custom Modernizr builds to detect features where required.
* Never user `<table>` elements for layout.
* Use `<p>` elements for consistent and design-appropriate copy spacing.
* Use semantic data/information formatting elements where appropriate:
	* `<dl>`, `<ul>` and `<ol>` elements for lists, dependent on the goal of the content.
	* `<table>` elements for displaying larger sets of or heavily inter-linked data.
	* `<thead>` and `<tbody>` elements to add further context to tables.
* All forms should have `<label>` elements properly associated with each field to support accessibility and provide context.
* Wrap large container elements (`<div>`, `<section>` etc.) in comments to denote beginning and end, making identifying closing tags much quicker. e.g.
```
<!-- my-wrapper start -->
<div class="my-wrapper">
	...
</div>
<!-- my-wrapper end -->
```
* Always quote HTML attributes.
* All markup should be delivered as UTF-8, as it's the most friendly for internationalization.

##CSS
True use the SASS preprocessor in all new projects, as such this section is short and most styling guidelines can be found in the [SASS](#sass) section.


* Style tags and attributes should not be used in page markup.
* CSS files should be minified in production environments and included in the `<head>` of any document.

##SASS
True's chosen preprocessor is SASS (SCSS style) with a custom, susy-based framework as a boilerplate for most projects. The framework can be found in the [Github repo](https://github.com/truedigital/frontend).

* Consider the use of `:before` and `:after` pseudo-elements over additional markup where possible, particularly when the style is not a necessity.
* Declare styles in a multi-line format to support readability and version control, write properties grouped by type (font, layout, animation etc.)
* We use BEM for clarity and sustainability of selectors, though not so strictly as to nest multiple Block selectors. e.g.
```
.my-block {
	&__element {
		&--modifier {

		}
	}

	&__another-element {

	}

	&--modifier {

	}
}
```
* Avoid over-qualifying selectors (`div.my-block`) as doing so causes issues with overriding styles in the future.
	* Similiarly, avoid unnecessary nesting of selectors. In a situation where you absolutely must nest an element within a class (CMS rich content), only do so on one level, i.e. `.my-block { a { ... } }`
* Write selectors and classes to reflect the function or identity of a block rather than the appearance, the appearance may change and invalidate the class.
* Using `!important` reactively, e.g. to get yourself out of nasty specificity situations, is not advised. Rework your CSS and try to combat these issues by refactoring your selectors.
* Avoid magic numbers. If an arbitrary value (e.g. 37%) positions an element how you want it to in a particular situation, it will almost certainly not be sustainable or consistent in future and has no meaning to anyone reading the code.
* When debugging, take code away before you start adding more to fix it. The problem will exist in the CSS that is already written, more CSS isn't necessarily the right answer!
* `@extend` can cause excessive bloat within compiled CSS files and is best avoided unless you are familiar with the output. Often similar results can be achieved with `@include` or stacking classes.

###Animation
* Do not do animations in jQuery.
* Use jQuery to add classes to trigger CSS animations, or use [Velocity](http://julian.com/research/velocity/)
* Favour transitions over animations.
* Where possible, avoid animating other properties than opacity and transform.
```
// Bad
.my-element:hover {
	animation: move 1s forwards;
}
@keyframes move {
	100% {
		margin-left: 100px;
	}
}

// Good
.my-element {
	transition: 1s;
}
.my-element:hover {
  	transform: translateX(100px);
}
```

##Images
* Images should be named in hyphenated style with descriptions making their usage obvious, e.g. `map-marker.png`, `map-marker_alt.png`
* Use [kraken.io](http://kraken.io) to reduce asset file size for static images

##Javascript
* 99% of JavaScript should be included in external JavaScript files and included at the END of the BODY tag. One exception to this rule is Modernizr which should be included at the end of the `<head>`.
* Name variables and functions logically and in camelCase. Sensible names that are long are preferred to short names that make no sense.
* Try to write functions to follow the principle that they should do one thing and do it well. If you can see that a function is becoming more complex, abstract it out into multiple functions – it will become more readable, reusable and will make more sense to someone unfamiliar to the code.
* Constants or configuration variables should be at the start of a class and written in CAPS, words separated by underscores.
* Use braces in condition structures unless they are single line statements, e.g.
```
// Bad
if(foo && foo.bar && foo.bar > 10)
    foo.baz = foo.bar - 100 * 2.7 + 'rad'

// Good
if (i < 10) return true;

// Good
if(foo && foo.bar && foo.bar > 10) {
    foo.baz = foo.bar - 100 * 2.7 + 'rad'
}
```
* Remap `this` to a variable called `self` when passing context.
* Always use === as a comparator (unless you really need flexible evaluations e.g comparison to null).
* When creating functions with many parameters, pass in an object rather than listing numerous parameters;
	* Use `$.extend` if you are using jQuery to extend a passed in object while providing defaults.
* Before including libraries (jQuery, Slick) in a project, consider if they are actually required rather than treating them as boilerplate website tools.

### jQuery
* Always cache DOM selections if you plan to re-use data
* Prefix jQuery collection variables with the dollar ($) character e.g `var $headerChildren = $('header').children()`
* Avoid using $.each for repeated or performance critical functionality. Instead use a for or reverse while loop (especially for large objects)
* Use `on()` and `off()` handlers for events. Everything else is now deprecated (live, delegate, bind).
* Recognise how `$element.data('value')` and `$element.attr('data-value')` differ.

###Object and Class Structure
####Singleton Object
This is, for all intents and purposes, a singleton object. This can be used when you need to create a component once per page, in this instance we are creating a "Tray" which slides in from the bottom of the screen and should be globally accessible such that content may be injected into it in a manner it controls.

```
var PreviewTray = (function($, Tray) {
	var LOCAL_CONSTANT = 100,
		localVariable = 'changeable';

	Tray.property = 'some value';
	Tray.refresh = function() {
		...
	}

    return Tray;
})(jQuery, PreviewTray || {});
```
####Prototype Approach
The prototype approach can be used to create what would be more comparable to an OO 'Class'. Multiple instances of the below could be created, independent of each other, without duplicating function instances.
```
var HorizontalScrollPanel = (function($){
	function HorizontalScrollPanel($wrap) {
		// Public properties populated on creation
		this.$wrap = $wrap;
		this.$plane = $wrap.find('[data-component="plane"]');
	}

	// Prototype methods
	HorizontalScrollPanel.prototype.resize = function() {
		...
	};

	HorizontalScrollPanel.prototype.rebuild = function() {
		...
	};

	return HorizontalScrollPanel;
})(jQuery)

// Create instances
var $horizontalScrollerElements = $('[data-component="horizontal-scroll-wrap"]'),
	horizontalScrollers = [];

for (var i in $horizontalScrollers) {
	horizontalScrollers.push(new HorizontalScrollPanel($horizontalScrollers[i]));
}
```

<!-- ##Gulp and Build Process
TODO: Description, setup advice?

##Responsive Design Methodology
TODO: Description

##Accessibility -->