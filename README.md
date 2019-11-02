# Bootstrap Tourist

Quick and easy way to build your product tours with Bootstrap Popovers for Bootstrap 3 and 4.

## About Bootstrap Tourist
Bootstrap Tourist (called "Tourist" from here) is a fork of Bootstrap Tour, a plugin to create product tours.

The original Bootstrap Tour was written in coffeescript, and had a number of open feature and bug fix requests in the github repo. Bootstrap Tourist is an in-progress effort to move Bootstrap Tour to native ES6, fix some issues and add some requested features. You can read more about why Bootstrap Tourist exists, and why it's not a github fork anymore, here: https://github.com/sorich87/bootstrap-tour/issues/713

This repo has been created to give an easy way to update and track the revisions to the code, rather than filling up the original Tour forum.


Tourist works with Bootstrap 3 and 4 (specify "framework" option), however the "standalone" non-Bootstrap version is not available

## Changelog from previous version:

Changes in v0.3.0
- MAJOR REVISION: new method of backdrop and highlight added, with major effort and code from @ibastevan.

Full changelog can be found in the top of bootstrap-tourist.js

v0.3.0 represents a significant change to Bootstrap Tourist.

Tourist is still completely drop-in replacement compatible with original Tour,
however @ibastevan and @IGreatlyDislikeJavascript have made significant changes
to how the backdrop works. You can read the conversation here:
https://github.com/IGreatlyDislikeJavascript/bootstrap-tourist/pull/24

In short, previous versions of Tourist (and original Tour) used 4 divs to bracket
the tour step elements.

From v0.3.0, Tourist now uses overlays to produce the same effect. This solves
many problems and enables many new features, including customizable transitions
between tour steps.

There may be residual bugs from this change so please report them in
the repo!

## Getting started with Bootstrap Tourist
Simply include bootstrap-tourist.js and bootstrap-tourist.css into your page. A minified version is not provided because the entire purpose of this repo is to enable fixes, features and native port to ES6. If you are uncomfortable with this, please use the original Bootstrap Tour!

```html
<link href="bootstrap-tourist.css" rel="stylesheet">
....
<script src="bootstrap-tourist.js"></script>
```

Next, set up and start your tour with some steps:
```javascript
var tour = new Tour({
						framework: "bootstrap3",	// or "bootstrap4" depending on your version of bootstrap
						steps:
						[
							{
							element: "#my-element",
							title: "Title of my step",
							content: "Content of my step"
							},
							{
							element: "#my-other-element",
							title: "Title of my step",
							content: "Content of my step"
							}
						]
					});

// Start the tour - note, no call to .init() required
tour.start();
```

## Switching from Tour to Tourist
If you already have a working tour using Bootstrap Tour, and you want to move to Tourist (because it has some fixes etc), perform the following steps:

1. Copy over the Tourist CSS and JS, include them instead of Bootstrap Tour
2. If you are using Bootstrap 4, add the following option to your initialization code:

```framework: "bootstrap4"```

For example:

```
var tour = new Tour({
						name: "tourist",
						steps:[...steps go here...],
						debug: true,	// you may wish to turn on debug for the first run through
						framework: "bootstrap4", // set Tourist to use BS4 compatibility
					});	
```

3. Remove the call to tour.init() - this is not required

4. Add a call to tour.start() to start the tour, and optionally add a call to tour.restart() to force restart the tour


## Basic Demo and Documentation
Tourist now has documentation included in the repo /docs/ folder. Take a look!

# Documentation note
In bootstrap-tourist.js, you will find comprehensive documentation and examples at the very top of the file. 

## Added features & fixes: Summary
The entire purpose of Tourist is to add features and fixes, and migrate to native ES6. Here is a list of all additional features and fixes in Tourist (compared to original Tour). All original Tour options are still available, unless superseded by the documentation below:

 1. onNext/onPrevious - prevent auto-move to next step, allow .goTo
 2. *** Do not call Tour.init *** - fixed tours with hidden elements on page reload
 3. Dynamically determine step element by function
 4. Only continue tour when reflex element is clicked using reflexOnly
 5. Call onElementUnavailable if step element is missing
 6. Scroll flicker/continual step reload fixed
 7. Magic progress bar and progress text, plus options to customize per step
 8. Prevent user interaction with element using preventInteraction
 9. Wait for arbitrary DOM element to be visible before showing tour step/crapping out due to missing element, using delayOnElement
 10. Handle bootstrap modal dialogs better - autodetect modals or children of modals, and call onModalHidden to handle when user dismisses modal without following tour steps
 11. Automagically fixes drawing issues with Bootstrap Selectpicker (https://github.com/snapappointments/bootstrap-select/)
 12. Call onPreviouslyEnded if tour.start() is called for a tour that has previously ended (see docs)
 13. Switch between Bootstrap 3 or 4 (popover template) automatically using tour options
 14. Added sanitizeWhitelist and sanitizeFunction global options, fixed Bootstrap 3.4.1 breaking change
 15. Added support for changing button texts 

from v0.3.0:

 16. Added showIfUnintendedOrphan to show a tour step as an orphan if its element doesn't exist, overriding onElementUnavailable
 17. Overlay divs and customizable transitions between tour steps


## Contributing
Feel free to contribute with pull requests, bug reports or enhancement suggestions.


## License

Code licensed under the [MIT license](https://opensource.org/licenses/MIT).
Documentation licensed under [CC BY 3.0](http://creativecommons.org/licenses/by/3.0/).
