# Bootstrap Tourist

Quick and easy way to build your product tours with Bootstrap Popovers for Bootstrap 3 and 4.

## About Bootstrap Tourist
Bootstrap Tourist (called "Tourist" from here) is a fork of Bootstrap Tour, a plugin to create product tours.

The original Bootstrap Tour was written in coffeescript, and had a number of open feature and bug fix requests in the github repo. Bootstrap Tourist is an in-progress effort to move Bootstrap Tour to native ES6, fix some issues and add some requested features. You can read more about why Bootstrap Tourist exists, and why it's not a github fork anymore, here: https://github.com/sorich87/bootstrap-tour/issues/713

This repo has been created to give an easy way to update and track the revisions to the code, rather than filling up the original Tour forum.


Tourist works with Bootstrap 3 and 4 (specify "framework" option), however the "standalone" non-Bootstrap version is not available

## Changelog from previous version:

Changes IN v0.2.0:
- Version update as major fix to bug preventing element: function(){...} feature under BS4/popper.js
- published as release

Full changelog can be found in the top of bootstrap-tourist.js


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
Because Tourist is based entirely on the original Bootstrap Tour, please see the demos and API documentation at the Bootstrap Tour website: [http://bootstraptour.com/api](http://bootstraptour.com/api)


# Documentation note
In bootstrap-tourist.js, you will find comprehensive documentation at the very top of the file. This is the most up to date and relevant docs, please look here first. The docs below in this readme are here for ease of reference only.


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


## Added features & fixes: Documentation
Full feature documentation below. These supersede any features or structure required by Bootstrap Tour.


### Control flow from onNext() / onPrevious() options:
Returning false from onNext/onPrevious handler will prevent Tour from automatically moving to the next/previous step.
Tour flow methods (Tour.goTo etc) now also work correctly in onNext/onPrevious.
Option is available per step or globally:

```
var tourSteps = [
					{
						element: "#inputBanana",
						title: "Bananas!",
						content: "Bananas are yellow, except when they're not",
						onNext: function(tour){
							if($('#inputBanana').val() !== "banana")
							{
								// no banana? highlight the banana field
								$('#inputBanana').css("background-color", "red");
								// do not jump to the next tour step!
								return false;
							}
						}
					}
				];

var Tour=new Tour({
					steps: tourSteps,
					framework: "bootstrap3",	// or "bootstrap4" depending on your version of bootstrap
					onNext: function(tour)
							{
								if(someVar = true)
								{
									// force the tour to jump to slide 3
									tour.goTo(3);
									// Prevent default move to next step - important!
									return false;
								}
							}
				});
```

### Do not call Tour.init if you previously used Bootstrap Tour
When setting up Tour, do not call Tour.init().
Call Tour.start() to start/resume the Tour from previous step
Call Tour.restart() to always start Tour from first step

Tour.init() was a redundant method that caused conflict with hidden Tour elements.

As of Tourist v0.11, calling Tour.init() will generate a warning in the console (thanks to @pau1phi11ips).

### Dynamically determine element by function
Step "element:" option allows element to be determined programmatically. Return a jquery object.
The following is possible:
```
var tourSteps = [
					{
						element: function() { return $(document).find("...something..."); },
						title: "Dynamic",
						content: "Element found by function"
					},
					{
						element: "#static",
						title: "Static",
						content: "Element found by static ID"
					}
				];
```


### Only continue tour when reflex element is clicked
Use step option reflexOnly in conjunction with step option reflex to automagically hide the "next" button in the tour, and only continue when the user clicks the element:
```
var tourSteps = [
					{
						element: "#myButton",
						reflex: true,
						reflexOnly: true,
						title: "Click it",
						content: "Click to continue, or you're stuck"
					}
				];
```


### Call function when element is missing
If the element specified in the step (static or dynamically determined as per feature #3), onElementUnavailable is called.
Function signature: function(tour, stepNumber) {}
Option is available at global and per step levels.

Use it per step to have a step-specific error handler:
```
function tourStepBroken(tour, stepNumber)
{
	alert("Uhoh, the tour broke on the #btnMagic element);
}

var tourSteps = [
					{
						element: "#btnMagic",
						onElementUnavailable: tourStepBroken,
						title: "Hold my beer",
						content: "now watch this"
					}
				];
```


Use it globally, and optionally override per step, to have a robust and comprehensive error handler:
```
function tourBroken(tour, stepNumber)
{
	alert("The default error handler: tour element is done broke on step number " + stepNumber);
}

var tourSteps = [
					{
						element: "#btnThis",
						//onElementUnavailable: COMMENTED OUT, therefore default global handler used
						title: "Some button",
						content: "Some content"
					},
					{
						element: "#btnThat",
						onElementUnavailable: 	function(tour, stepNumber)
												{
													// override the default global handler for this step only
													alert("The tour broke on #btnThat step");
												},
						title: "Another button",
						content: "More content"
					}
				];

var Tour=new Tour({
					steps: tourSteps,
					framework: "bootstrap3",	// or "bootstrap4" depending on your version of bootstrap
					onElementUnavailable: tourBroken, // default "element unavailable" handler for all tour steps
				});
```


### Scroll flicker / continue reload fixed
Original Tour constantly reloaded the current tour step on scroll & similar events. This produced flickering, constant reloads and therefore constant calls to all the step function calls.
This is now fixed. Scrolling the browser window does not cause the tour step to reload.

IMPORTANT: orphan steps are stuck to the center of the screen. However steps linked to elements ALWAYS stay stuck to their element, even if user scrolls the element & tour popover
			off the screen. This is my personal preference, as original functionality of tour step moving with the scroll even when the element was off the viewport seemed strange.



### Progress bar & progress text:
With thanks to @macroscian, @thenewbeat for fixes to this code, incorporated in Tourist v0.12
Use the following options globally or per step to show tour progress:
showProgressBar - shows a bootstrap progress bar for tour progress at the top of the tour content
showProgressText - shows a textual progress (N/X, i.e.: 1/24 for slide 1 of 24) in the tour title

```
var tourSteps = [
					{
						element: "#inputBanana",
						title: "Bananas!",
						content: "Bananas are yellow, except when they're not",
					},
					{
						element: "#inputOranges",
						title: "Oranges!",
						content: "Oranges are not bananas",
						showProgressBar: false,	// don't show the progress bar on this step only
						showProgressText: false, // don't show the progress text on this step only
					}
				];
				
var Tour=new Tour({
					steps: tourSteps,
					framework: "bootstrap3",	// or "bootstrap4" depending on your version of bootstrap
					showProgressBar: true, // default show progress bar
					showProgressText: true, // default show progress text
				});
```


### Customize the progressbar/progress text:
In conjunction with previous, provide the following functions globally or per step to draw your own progressbar/progress text:

```
getProgressBarHTML(percent)
getProgressTextHTML(stepNumber, percent, stepCount)
```

These will be called when each step is shown, with the appropriate percentage/step number etc passed to your function. Return an HTML string of a "drawn" progress bar/progress text
which will be directly inserted into the tour step.

Example:
```
var tourSteps = [
					{
						element: "#inputBanana",
						title: "Bananas!",
						content: "Bananas are yellow, except when they're not",
					},
					{
						element: "#inputOranges",
						title: "Oranges!",
						content: "Oranges are not bananas",
						getProgressBarHTML:	function(percent)
											{
												// override the global progress bar function for this step
												return '<div>You be ' + percent + ' of the way through!</div>';
											}
					}
				];
var Tour=new Tour({
					steps: tourSteps,
					framework: "bootstrap3",	// or "bootstrap4" depending on your version of bootstrap
					showProgressBar: true, // default show progress bar
					showProgressText: true, // default show progress text
					getProgressBarHTML: 	function(percent)
											{
												// default progress bar for all steps. Return valid HTML to draw the progress bar you want
												return '<div class="progress"><div class="progress-bar progress-bar-striped" role="progressbar" style="width: ' + percent + '%;"></div></div>';
											},
					getProgressTextHTML: 	function(stepNumber, percent, stepCount)
											{
												// default progress text for all steps
												return 'Slide ' + stepNumber + "/" + stepCount;
											},

				});
```



### Prevent interaction with element
Sometimes you want to highlight a DOM element (button, input field) for a tour step, but don't want the user to be able to interact with it.
Use preventInteraction to stop the user touching the element:

```
var tourSteps = [
					{
						element: "#btnMCHammer",
						preventInteraction: true,
						title: "Hammer Time",
						content: "You can't touch this"
					}
				];
```


### Wait for an element to appear before continuing tour
Sometimes a tour step element might not be immediately ready because of transition effects etc. This is a specific issue with bootstrap select, which is relatively slow to show the selectpicker
dropdown after clicking.
Use delayOnElement to instruct Tour to wait for **ANY** element to appear before showing the step (or crapping out due to missing element). Yes this means the tour step element can be one DOM
element, but the delay will wait for a completely separate DOM element to appear. This is really useful for hidden divs etc.
Use in conjunction with onElementUnavailable for robust tour step handling.
By default this element must be present in DOM, but it should be hidden. If you want to wait for a new element to appear, use the `includeHidden` option.

delayOnElement is an object with the following:
```
				delayOnElement: {
									delayElement: "#waitForMe", // the element to wait to become visible, or the string literal "element" to use the step element
									maxDelay: 2000, // optional milliseconds to wait/timeout for the element, before crapping out. If maxDelay is not specified, this is 2000ms by default,
									includeHidden: false // optional, false if the element is already in the page, or true if the element will appear later
								}
```

Example:
```
var tourSteps = [
					{
						element: "#btnPrettyTransition",
						delayOnElement:	{
											delayElement: "element" // use string literal "element" to wait for this step's element, i.e.: #btnPrettyTransition
										},
						title: "Ages",
						content: "This button takes ages to appear"
					},
					{
						element: "#inputUnrelated",
						delayOnElement:	{
											delayElement: "#divStuff" // wait until DOM element "divStuff" is visible before showing this tour step against DOM element "inputUnrelated"
										},
						title: "Waiting",
						content: "This input is nice, but you only see this step when the other div appears"
					},
					{
						element: "#btnDontForgetThis",
						delayOnElement:	{
											delayElement: "element", // use string literal "element" to wait for this step's element, i.e.: #btnDontForgetThis
											maxDelay: 5000	// wait 5 seconds for it to appear before timing out
										},
						title: "Cool",
						content: "Remember the onElementUnavailable option!",
						onElementUnavailable: 	function(tour, stepNumber)
												{
													// This will be called if btnDontForgetThis is not visible after 5 seconds
													console.log("Well that went badly wrong");
												}
					},
				];
```


### Trigger when modal closes
If tour element is a modal, or is a DOM element inside a modal, the element can disappear "at random" if the user dismisses the dialog.
In this case, onModalHidden global and per step function is called. Only functional when step is not an orphan.
This is useful if a tour includes a step that launches a modal, and the tour requires the user to take some actions inside the modal before OK'ing it and moving to the next
tour step.

- Return (int) step number to immediately move to that step
- Return exactly false to not change tour state in any way - this is useful if you need to reshow the modal because some validation failed
- Return anything else to move to the next step

element === Bootstrap modal, or element parent === bootstrap modal is automatically detected.

```
var Tour=new Tour({
					steps: tourSteps,
					framework: "bootstrap3",	// or "bootstrap4" depending on your version of bootstrap
					onModalHidden: 	function(tour, stepNumber)
									{
										console.log("Well damn, this step's element was a modal, or inside a modal, and the modal just done got dismissed y'all. Moving to step 3.");

										// move to step number 3
										return 3;
									},
				});


var Tour=new Tour({
					steps: tourSteps,
					framework: "bootstrap3",	// or "bootstrap4" depending on your version of bootstrap
					onModalHidden: 	function(tour, stepNumber)
									{
										if(validateSomeModalContent() == false)
										{
											// The validation failed, user dismissed modal without properly taking actions.
											// Show the modal again
											showModalAgain();

											// Instruct tour to stay on same step
											return false;
										}
										else
										{
											// Content was valid. Return null or do nothing to instruct tour to continue to next step
										}
									},
				});
```


### Handle Dialogs and BootstrapDialog plugin better (https://nakupanda.github.io/bootstrap3-dialog/)
Plugin makes creating dialogs very easy, but it does some extra stuff to the dialogs and dynamically creates/destroys them. This
causes issues with plugins that want to include a modal dialog in the steps using this plugin.

To use Tour to highlight an element in a dialog, just use the element ID as you would for any normal step. The dialog will be automatically
detected and handled.

To use Tour to highlight an entire dialog, set the step element to the dialog div. Tour will automatically realize this is a dialog, and
shift the element to use the modal-content div inside the dialog. This makes life friendly, because you can do this:

```
<div class="modal" id="myModal" role="dialog">
	<div class="modal-dialog">
		<div class="modal-content">
		...blah...
		</div>
	</div>
</div>
```

Then use element: myModal in the Tour.


FOR BOOTSTRAPDIALOG PLUGIN: this plugin creates random UUIDs for the dialog DOM ID. You need to fix the ID to something you know. Do this:

```
dlg = new BootstrapDialog.confirm({
									....all the options...
								});

// BootstrapDialog gives a random GUID ID for dialog. Give it a proper one
$objModal = dlg.getModal();
$objModal.attr("id", "myModal");
dlg.setId("myModal");
```

Now you can use element: myModal in the tour, even when the dialog is created by BootstrapDialog plugin.



###	Fix conflict with Bootstrap Selectpicker: https://github.com/snapappointments/bootstrap-select/
Selectpicker draws a custom select. Tour now automagically finds and adjusts the selectpicker dropdown so that it appears correctly within the tour



### Call onPreviouslyEnded if tour.start() is called for a tour that has previously ended
See the following github issue: https://github.com/sorich87/bootstrap-tour/issues/720
Original behavior for a tour that had previously ended was to call onStart() callback, and then abort without calling onEnd(). This has been altered so
that calling start() on a tour that has previously ended (cookie step set to end etc) will now ONLY call onPreviouslyEnded().

This restores the functionality that allows app JS to simply call tour.start() on page load, and the Tour will now only call onStart() / onEnd() when
the tour really is started or ended.

```
var Tour=new Tour({
					steps: [ ..... ],
					framework: "bootstrap3",	// or "bootstrap4" depending on your version of bootstrap
					onPreviouslyEnded: 	function(tour)
										{
											console.log("Looks like this tour has already ended");
										},
				});

Tour.start();
```


### Switch between Bootstrap 3 or 4 (popover template) automatically using tour options, or use a custom template
With thanks to this thread: https://github.com/sorich87/bootstrap-tour/pull/643

Tour is compatible with bootstrap 3 and 4 if the right template and framework is used for the popover. Bootstrap3 framework compatibility is used by default.

To select the correct template and framework, use the "framework" global option. Note this option does more than just select a template, it also changes which methods are used to manage the Tour popovers to be BS3 or BS4 compatible.

```
var Tour=new Tour({
					steps: tourSteps,
					template: null,			// template option is null by default. Tourist will use the appropriate template
											// for the framework version, in this case BS3 as per next option
					framework: "bootstrap3" // can be string literal "bootstrap3" or "bootstrap4"
				});
```

To use a custom template, use the "template" global option:

```
var Tour=new Tour({
					steps: tourSteps,
					framework: "bootstrap4",	// or "bootstrap3" depending on your version of bootstrap
					template: '<div class="popover" role="tooltip">....blah....</div>'
				});
```

Review the following logic:
- If template == null, default framework template is used based on whether framework is set to "bootstrap3" or "bootstrap4"
- If template != null, the specified template is always used
- If framework option is not literal "bootstrap3" or "bootstrap4", error will occur


To add additional templates, search the code for "PLACEHOLDER: TEMPLATES LOCATION". This will take you to an array that contains the templates, simply edit
or add as required.


### Options to manipulate the Bootstrap sanitizer, and fix the sanitizer related breaking change in BS 3.4.x
BS 3.4.1 added a sanitizer to popover and tooltips - this breaking change strips non-whitelisted DOM elements from popover content, title etc.
See: https://getbootstrap.com/docs/3.4/javascript/#js-sanitizer and https://blog.getbootstrap.com/2019/02/13/bootstrap-4-3-1-and-3-4-1/

This Bootstrap change resulted in Tour navigation buttons being killed from the DOM: https://github.com/sorich87/bootstrap-tour/issues/723#issuecomment-471107788

This has been fixed in code, Tour navigation buttons now appear and work by default.

To prevent future similar reoccurrences, and also allow the manipulation of the sanitizer "allowed list" for Tours that want to add extra content into
tour steps, two features added to global options. To understand the purpose and operation of these features, review the following information on the Bootstrap
sanitizer: https://getbootstrap.com/docs/3.4/javascript/#js-sanitizer

--IMPORTANT NOTE-- SECURITY RISK: if you do not understand the purpose of the sanitizer, why it exists in bootstrap or how it relates to Tour, do not use these options.

Global options:

	sanitizeWhitelist:	specify an object that will be merged with the Bootstrap Popover default whitelist. Use the same structure as the default Bootstrap
						whitelist.

	sanitizeFunction:	specify a function that will be used to sanitize Tour content, with the following signature: string function(content).
						Specifying a function for this option will cause sanitizeWhitelist to be ignored.
						Specifying anything other than a function for this option will be ignored, and sanitizeWhitelist will be used

Examples:

Allow tour step content to include a button with attributes data-someplugin1="..." and data-somethingelse="...". Allow content to include a selectpicker.

```
var Tour=new Tour({
					steps: tourSteps,
					sanitizeWhitelist:	{
											"button"	: ["data-someplugin1", "data-somethingelse"],	// allows <button data-someplugin1="abc", data-somethingelse="xyz">
											"select"	: []											// allows <select>
										}
				});
```


Use a custom whitelist function for sanitizing tour steps:
```
var Tour=new Tour({
					steps: tourSteps,
					sanitizeFunction:	function(stepContent)
										{
											// Bypass Bootstrap sanitizer using custom function to clean the tour step content.
											// stepContent will contain the content of the step, i.e.: tourSteps[n].content. You must
											// clean this content to prevent XSS and other vulnerabilities. Use your own code or a lib like DOMPurify
											return DOMPurify.sanitize(stepContent);
										}
				});
```

Note: if you have complete control over the tour content (i.e.: no risk of XSS or similar attacks), you can use sanitizeFunction to bypass all sanitization and use your step content exactly as is by simply returning the content:

```
var Tour=new Tour({
					steps: tourSteps,
					framework: "bootstrap3",	// or "bootstrap4" depending on your version of bootstrap
					sanitizeFunction:	function(stepContent)
										{
											// POTENTIAL SECURITY RISK
											// bypass Bootstrap sanitizer, perform no sanitization, tour step content will be exactly as templated in tourSteps.
											return stepContent;
										}
				});
```

### Change text for the buttons in the popup
(also, preparation for future localization options)
With thanks to @vneri (https://github.com/IGreatlyDislikeJavascript/bootstrap-tourist/pull/8) for the original change
With thanks to @DancingDad, @thenewbeat, @bardware for the fixes/updates
You can now change the text displayed for the buttons used in the tour step popups.	For this, there is a new object you can pass to the options, called "localization".
This option only applies to the default templates. If you specify your own custom template, the localization.buttonTexts option has no effect on the basis that you will make any changes to your own template directly.

```
var tour = new Tour({
						framework: "bootstrap3",	// or "bootstrap4" depending on your version of bootstrap
						steps:
						[ .... ],
						localization:
						{
							buttonTexts:	{
												prevButton: 'Back',
												nextButton: 'Go',
												pauseButton: 'Wait',
												resumeButton: 'Continue',
												endTourButton: 'Ok, enough'
											}
						}

					});
```

You may specify only the labels you want to change. Unspecified labels will remain at their defaults:

```
	var tour = new Tour({
							localization:
							{
								buttonTexts:	{
													endTourButton: 'Adios muchachos'
												}
							}
						});
```


## Contributing
Feel free to contribute with pull requests, bug reports or enhancement suggestions.


## License

Code licensed under the [MIT license](https://opensource.org/licenses/MIT).
Documentation licensed under [CC BY 3.0](http://creativecommons.org/licenses/by/3.0/).
