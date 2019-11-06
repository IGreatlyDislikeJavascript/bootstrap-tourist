# Bootstrap Tourist [![NPM Version](http://img.shields.io/npm/v/bootstrap-tourist.svg?style=flat)](https://www.npmjs.org/)

Quick and easy way to build your product tours with Bootstrap Popovers for Bootstrap 3 and 4.

## About Bootstrap Tourist
Bootstrap Tourist (called "Tourist" from here) is a fork of Bootstrap Tour, a plugin to create product tours.

The original Bootstrap Tour was written in coffeescript, and had a number of open feature and bug fix requests in the github repo. Bootstrap Tourist is an in-progress effort to move Bootstrap Tour to native ES6, fix some issues and add some requested features. You can read more about why Bootstrap Tourist exists, and why it's not a github fork anymore, here: https://github.com/sorich87/bootstrap-tour/issues/713

Tourist works with Bootstrap 3.4 and 4.3 (specify "framework" option), however the "standalone" non-Bootstrap version is not available

## Minimum Bootstrap / jQuery requirements
There are some bugs in earlier BS3 and BS4, and jquery versions, that cause problems with Tourist. Please use minimum Bootstrap 3.4.x or 4.3.x, jquery 3.3.1 to avoid. Earlier versions may work, but try for yourself.

## Getting started with Bootstrap Tourist
If you are new to Bootstrap Tourist, and don't have Bootstrap Tour working, it's as simple as doing the following:
1. Simply include bootstrap-tourist.js and bootstrap-tourist.css into your page:
    ```html
    <link href="bootstrap-tourist.css" rel="stylesheet">

    ...

    <script src="bootstrap-tourist.js"></script>
    ```
1. Next, set up and start your tour with some steps:
    ```js
    var tour = new Tour({
        framework: 'bootstrap3',   // or "bootstrap4" depending on your version of bootstrap
        steps: [
            {
                element: '#my-element',
                title: 'Title of my step',
                content: 'Content of my step'
            },
            {
                element: '#my-other-element',
                title: 'Title of my step',
                content: 'Content of my step'
            }
        ]
    });

    // Start the tour - note, no call to .init() is required
    tour.start();
    ```

**NOTE**: A minified version is not provided because the entire purpose of this repo is to enable fixes, features and native port to ES6. If you are uncomfortable with this, please use the original Bootstrap Tour!

## Switching from Tour to Tourist
If you already have a working tour using Bootstrap Tour, and you want to move to Tourist (because it has some fixes etc), perform the following steps:
1. Copy over the Tourist CSS and JS, include them instead of Bootstrap Tour
    - If you are using Bootstrap 4, add the `framework: 'bootstrap4'` option to your initialization code.
    - Example:
        ```js
        const tour = new Tour({
            name: 'tourist',
            steps: [...steps go here...],
            debug: true,               // you may wish to turn on debug for the first run through
            framework: 'bootstrap4',   // set Tourist to use BS4 compatibility
        });
        ```
1. Remove the call to `tour.init()` - this is not required
1. Add a call to `tour.start()` to start the tour, and optionally add a call to `tour.restart()` to force restart the tour

## Basic Demo and Documentation
Tourist now has documentation included in the repo under the `/docs/` folder. Take a look!
1. Control flow from `onNext()` / `onPrevious()` options:
   - Returning false from onNext/onPrevious handler will prevent Tour from automatically moving to the next/previous step.
   - Tour flow methods (Tour.goTo etc) now also work correctly in onNext/onPrevious.
   - Option is available per step or globally:
    ```js
    var tourSteps = [
        {
            element: "#inputBanana",
            title: "Bananas!",
            content: "Bananas are yellow, except when they're not",
            onNext: function (tour) {
                if ($("#inputBanana").val() !== "banana") {

                    // no banana? highlight the banana field
                    $("#inputBanana").css("background-color", "red");

                    // do not jump to the next tour step!
                    return false;
                }
            }
        }
    ];

    var tour = new Tour({
        steps: tourSteps,
        framework: "bootstrap3",   // or "bootstrap4" depending on your version of bootstrap
        buttonTexts: {             // customize or localize button texts
            nextButton: "go on",
            endTourButton: "ok it's over",
        },
        onNext: function(tour) {
            if (someVar = true) {
                // force the tour to jump to slide 3
                tour.goTo(3);
                // Prevent default move to next step - important!
                return false;
            }
        }
    });
    ```
1. Do not call `tour.init()`:
    - When setting up Tour, do not call `tour.init()`. Call `tour.start()` to start/resume the Tour from previous step. Call `tour.restart()` to always start Tour from the first step
    `tour.init()` was a redundant method that caused conflict with hidden Tour elements.
    - As of Tourist v0.11, calling `tour.init()` will generate a warning in the console (thanks to [@pau1phi11ips](https://github.com/pau1phi11ips)).
1. Dynamically determine element by function:
    - Step "element:" option allows element to be determined programmatically. Return a jquery object. The following is possible:
        ```js
        var tourSteps = [
            {
                element: function () {
                    return $(document).find(".something");
                },
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
1. Only continue tour when reflex element is clicked:
    - Use step option reflexOnly in conjunction with step option reflex to automagically hide the "next" button in the tour, and only continue when the user clicks the element:
        ```js
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
1. Call function when element is missing:
    - If the element specified in the step (static or dynamically determined as per feature #3), `onElementUnavailable` is called.
    - Function signature: `function(tour, stepNumber) {}`
    - Option is available at global and per step levels:
        - Use it per step to have a step-specific error handler:
            ```js
            function tourStepBroken(tour, stepNumber) {
                alert("Uhoh, the tour broke on the #btnMagic element");
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
        - Use it globally, and optionally override per step, to have a robust and comprehensive error handler:
            ```js
            function tourBroken(tour, stepNumber) {
                alert('The default error handler: tour element is done broke on step number ' + stepNumber);
            }

            var tourSteps = [
                {
                    element: "#btnThis",
                    // onElementUnavailable: COMMENTED OUT, therefore default global handler used
                    title: "Some button",
                    content: "Some content"
                },
                {
                    element: "#btnThat",
                    onElementUnavailable: function (tour, stepNumber) {
                        // override the default global handler for this step only
                        alert("The tour broke on #btnThat step");
                    },
                    title: "Another button",
                    content: "More content"
                }
            ];

            var tour = new Tour({
                steps: tourSteps,
                framework: "bootstrap3",    // or "bootstrap4" depending on your version of bootstrap
                onElementUnavailable: tourBroken, // default "element unavailable" handler for all tour steps
            });
            ```
1. Scroll flicker / continue reload fixed:
    - Original Tour constantly reloaded the current tour step on scroll & similar events. This produced flickering, constant reloads and therefore constant calls to all the step function calls. This is now fixed. Scrolling the browser window does not cause the tour step to reload.
    - **IMPORTANT**: orphan steps are stuck to the center of the screen. However steps linked to elements ALWAYS stay stuck to their element, even if user scrolls the element & tour popover off the screen. This is my personal preference, as original functionality of tour step moving with the scroll even when the element was off the viewport seemed strange.
1. Customizable progress bar & text
    1. Progress bar & progress text:
        - With thanks to [@macroscian](](https://github.com/macroscian), [@thenewbeat](](https://github.com/thenewbeat) for fixes to this code, incorporated in Tourist v0.12
        - Use the following options globally or per step to show tour progress:
            - `showProgressBar`: shows a bootstrap progress bar for tour progress at the top of the tour content
            - `showProgressText`: shows a textual progress (N/X, i.e.: 1/24 for slide 1 of 24) in the tour title
        - example:
            ```js
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
                    showProgressBar: false,     // don't show the progress bar on this step only
                    showProgressText: false,    // don't show the progress text on this step only
                }
            ];

            var tour = new Tour({
                framework: "bootstrap3",    // or "bootstrap4" depending on your version of bootstrap
                steps: tourSteps,
                showProgressBar: true,      // default show progress bar
                showProgressText: true,     // default show progress text
            });
            ```
    1. Customize the progressbar/progress text:
        - In conjunction with 7a, provide the following functions globally or per step to draw your own progressbar/progress text:
            - `getProgressBarHTML(percent)`
            - `getProgressTextHTML(stepNumber, percent, stepCount)`
        - These will be called when each step is shown, with the appropriate percentage/step number etc passed to your function. Return an HTML string of a "drawn" progress bar/progress text which will be directly inserted into the tour step.
        - example:
            ```js
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
                    getProgressBarHTML: function(percent) {
                        // override the global progress bar function for this step
                        return '<div>You\'re ' + percent + ' of the way through!</div>';
                    }
                }
            ];

            var tour = new Tour({
                steps: tourSteps,
                showProgressBar: true, // default show progress bar
                showProgressText: true, // default show progress text
                getProgressBarHTML: function(percent) {
                    // default progress bar for all steps. Return valid HTML to draw the progress bar you want
                    return '<div class="progress"><div class="progress-bar progress-bar-striped" role="progressbar" style="width: ' + percent + '%;"></div></div>';
                },
                getProgressTextHTML: function(stepNumber, percent, stepCount) {
                    // default progress text for all steps
                    return 'Slide ' + stepNumber + "/" + stepCount;
                }
            });
            ```
1. Prevent interaction with element
    - Sometimes you want to highlight a DOM element (button, input field) for a tour step, but don't want the user to be able to interact with it. Use preventInteraction to stop the user touching the element:
        ```js
        var tourSteps = [
            {
                element: "#btnMCHammer",
                preventInteraction: true,
                title: "Hammer Time",
                content: "You can't touch this"
            }
        ];
        ```
1. Wait for an element to appear before continuing tour:
    - Sometimes a tour step element might not be immediately ready because of transition effects etc. This is a specific issue with bootstrap select, which is relatively slow to show the selectpicker dropdown after clicking. Use delayOnElement to instruct Tour to wait for **ANY** element to appear before showing the step (or crapping out due to missing element). Yes this means the tour step element can be one DOM element, but the delay will wait for a completely separate DOM element to appear. This is really useful for hidden divs etc.
    - Use in conjunction with onElementUnavailable for robust tour step handling.
    - `delayOnElement` is an object with the following:
        ```js
        delayOnElement: {
            delayElement: "#waitForMe", // the element to wait to become visible, or the string literal "element" to use the step element, or a function
            maxDelay: 2000 // optional milliseconds to wait/timeout for the element, before crapping out. If maxDelay is not specified, this is 2000ms by default,
        }
        ```
    - example:
        ```js
        var tourSteps = [
            {
                element: "#btnPrettyTransition",
                delayOnElement: {
                    delayElement: "element" // use string literal "element" to wait for this step's element, i.e.: #btnPrettyTransition
                },
                title: "Ages",
                content: "This button takes ages to appear"
            },
            {
                element: "#btnPrettyTransition",
                delayOnElement: {
                    delayElement: function() {
                        return $("#btnPrettyTransition"); // return a jquery object to wait for, in this case #btnPrettyTransition
                    }
                },
                title: "Function",
                content: "This button takes ages to appear, we're waiting for an element returned by function"
            },
            {
                element: "#inputUnrelated",
                delayOnElement: {
                    delayElement: "#divStuff" // wait until DOM element "divStuff" is visible before showing this tour step against DOM element "inputUnrelated"
                },
                title: "Waiting",
                content: "This input is nice, but you only see this step when the other div appears"
            },
            {
                element: "#btnDontForgetThis",
                delayOnElement: {
                    delayElement: "element", // use string literal "element" to wait for this step's element, i.e.: #btnDontForgetThis
                    maxDelay: 5000  // wait 5 seconds for it to appear before timing out
                },
                title: "Cool",
                content: "Remember the onElementUnavailable option!",
                onElementUnavailable: function(tour, stepNumber) {
                    // This will be called if btnDontForgetThis is not visible after 5 seconds
                    console.log("Well that went badly wrong");
                }
            },
        ];
        ```
1. Trigger when modal closes:
    - If tour element is a modal, or is a DOM element inside a modal, the element can disappear "at random" if the user dismisses the dialog.
    - In this case, onModalHidden global and per step function is called. Only functional when step is not an orphan.
    - This is useful if a tour includes a step that launches a modal, and the tour requires the user to take some actions inside the modal before OK'ing it and moving to the next tour step:
        - Return (int) step number to immediately move to that step
        - Return exactly false to not change tour state in any way - this is useful if you need to reshow the modal because some validation failed
        - Return anything else to move to the next step
    - element === Bootstrap modal, or element parent === bootstrap modal is automatically detected.
    - example:
        ```js
        var tour = new Tour({
            steps: tourSteps,
            framework: "bootstrap3",    // or "bootstrap4" depending on your version of bootstrap
            onModalHidden: function(tour, stepNumber) {
                console.log("Well damn, this step's element was a modal, or inside a modal, and the modal just done got dismissed y'all. Moving to step 3.");

                // move to step number 3
                return 3;
            },
        });

        var tour = new Tour({
            steps: tourSteps,
            onModalHidden: function(tour, stepNumber) {
                if (validateSomeModalContent() == false) {
                    // The validation failed, user dismissed modal without properly taking actions.
                    // Show the modal again
                    showModalAgain();

                    // Instruct tour to stay on same step
                    return false;
                } else {
                    // Content was valid. Return null or do nothing to instruct tour to continue to next step
                }
            },
        });
        ```
1. Handle Dialogs and BootstrapDialog plugin better https://nakupanda.github.io/bootstrap3-dialog/
    - Plugin makes creating dialogs very easy, but it does some extra stuff to the dialogs and dynamically creates/destroys them. This causes issues with plugins that want to include a modal dialog in the steps using this plugin.
    - To use Tour to highlight an element in a dialog, just use the element ID as you would for any normal step. The dialog will be automatically detected and handled.
    - To use Tour to highlight an entire dialog, set the step element to the dialog div. Tour will automatically realize this is a dialog, and shift the element to use the modal-content div inside the dialog. This makes life friendly, because you can do this:
        ```html
        <div class="modal" id="myModal" role="dialog">
            <div class="modal-dialog">
                <div class="modal-content">
                ...blah...
                </div>
            </div>
        </div>
        ```
    - Then use element myModal in the Tour.
    - **FOR BOOTSTRAPDIALOG PLUGIN**:
        - This plugin creates random UUIDs for the dialog DOM ID. You need to fix the ID to something you know. Do this:
            ```js
            dlg = new BootstrapDialog.confirm({
                ...all the options...
            });

            // BootstrapDialog gives a random GUID ID for dialog. Give it a proper one
            $objModal = dlg.getModal();
            $objModal.attr("id", "myModal");
            dlg.setId("myModal");
            ```
        - Now you can use element myModal in the tour, even when the dialog is created by BootstrapDialog plugin.
1. Fix conflict with Bootstrap Selectpicker: https://github.com/snapappointments/bootstrap-select/
    - Selectpicker draws a custom select. Tour now automagically finds and adjusts the selectpicker dropdown so that it appears correctly within the tour
1. Call `onPreviouslyEnded` if `tour.start()` is called for a tour that has previously ended:
    - See the following github issue: https://github.com/sorich87/bootstrap-tour/issues/720
    - Original behavior for a tour that had previously ended was to call onStart() callback, and then abort without calling onEnd(). This has been altered so that calling start() on a tour that has previously ended (cookie step set to end etc) will now ONLY call onPreviouslyEnded().
    - This restores the functionality that allows app JS to simply call tour.start() on page load, and the Tour will now only call onStart() / onEnd() when
    the tour really is started or ended.
    - example:
        ```js
        var tour = new Tour({
            steps: [ ... ],
            framework: "bootstrap3",    // or "bootstrap4" depending on your version of bootstrap
            onPreviouslyEnded:  function(tour) {
                console.log("Looks like this tour has already ended");
            },
        });

        tour.start();
        ```
1. Switch between Bootstrap 3 or 4 (popover methods, template) automatically using tour options, or use a custom template:
    - With thanks to this thread: https://github.com/sorich87/bootstrap-tour/pull/643
    - Tour is compatible with bootstrap 3 and 4 if the right template and framework is used for the popover. Bootstrap3 framework compatibility is used by default.
    - To select the correct template and framework, use the "framework" global option. Note this option does more than just select a template, it also changes which methods are used to manage the Tour popovers to be BS3 or BS4 compatible.
        ```js
        var tour = new Tour({
            steps: tourSteps,
            template: null,         // template option is null by default. Tourist will use the appropriate template for the framework version, in this case BS3 as per next option
            framework: "bootstrap3", // can be string literal "bootstrap3" or "bootstrap4"
        });
        ```
    - To use a custom template, use the "template" global option:
        ```js
        var tour = new Tour({
            steps: tourSteps,
            framework: "bootstrap4", // can be string literal "bootstrap3" or "bootstrap4"
            template: '<div class="popover" role="tooltip">....blah....</div>'
        });
        ```
    - Review the following logic:
        - If template == null, default framework template is used based on whether framework is set to "bootstrap3" or "bootstrap4"
        - If template != null, the specified template is always used
        - If framework option is not literal "bootstrap3" or "bootstrap4", error will occur
    - To add additional templates, search the code for "PLACEHOLDER: TEMPLATES LOCATION". This will take you to an array that contains the templates, simply edit or add as required.
1. Options to manipulate the Bootstrap sanitizer, and fix the sanitizer related breaking change in BS 3.4.x:
    - BS 3.4.1 added a sanitizer to popover and tooltips - this breaking change strips non-whitelisted DOM elements from popover content, title etc.
    - See: https://getbootstrap.com/docs/3.4/javascript/#js-sanitizer and https://blog.getbootstrap.com/2019/02/13/bootstrap-4-3-1-and-3-4-1/
    - This Bootstrap change resulted in Tour navigation buttons being killed from the DOM: https://github.com/sorich87/bootstrap-tour/issues/723#issuecomment-471107788
    - This has been fixed in code, Tour navigation buttons now appear and work by default.
    - To prevent future similar reoccurrences, and also allow the manipulation of the sanitizer "allowed list" for Tours that want to add extra content into tour steps, two features added to global options. To understand the purpose and operation of these features, review the following information on the Bootstrap sanitizer: https://getbootstrap.com/docs/3.4/javascript/#js-sanitizer
    - _**IMPORTANT NOTE** SECURITY RISK_: if you do not understand the purpose of the sanitizer, why it exists in bootstrap or how it relates to Tour, do not use these options.
    - Global options:
        - `sanitizeWhitelist`: specify an object that will be merged with the Bootstrap Popover default whitelist. Use the same structure as the default Bootstrap whitelist.
        - `sanitizeFunction`:  specify a function that will be used to sanitize Tour content, with the following signature: string function(content). Specifying a function for this option will cause sanitizeWhitelist to be ignored. Specifying anything other than a function for this option will be ignored, and sanitizeWhitelist will be used
    - Examples:
        - Allow tour step content to include a button with attributes `data-someplugin1="..."` and `data-somethingelse="..."`. Allow content to include a selectpicker.
            ```js
            var tour=new Tour({
                steps: tourSteps,
                sanitizeWhitelist:  {
                    "button"    : ["data-someplugin1", "data-somethingelse"],   // allows <button data-someplugin1="abc", data-somethingelse="xyz">
                    "select"    : []                                            // allows <select>
                }
            });
            ```
        - Use a custom whitelist function for sanitizing tour steps:
            ```js
            var tour = new Tour({
                steps: tourSteps,
                sanitizeFunction: function(stepContent) {
                    // Bypass Bootstrap sanitizer using custom function to clean the tour step content.
                    // stepContent will contain the content of the step, i.e.: tourSteps[n].content. You must
                    // clean this content to prevent XSS and other vulnerabilities. Use your own code or a lib like DOMPurify
                    return DOMPurify.sanitize(stepContent);
                }
            });
            ```
        - Note: if you have complete control over the tour content (i.e.: no risk of XSS or similar attacks), you can use sanitizeFunction to bypass all sanitization and use your step content exactly as is by simply returning the content:
            ```js
            var tour = new Tour({
                steps: tourSteps,
                sanitizeFunction: function(stepContent) {
                    // POTENTIAL SECURITY RISK
                    // bypass Bootstrap sanitizer, perform no sanitization, tour step content will be exactly as templated in tourSteps.
                    return stepContent;
                }
            });
            ```
1. Change text for the buttons in the popup (also, preparation for future localization options):
    - With thanks to [@vneri](https://github.com/vneri) (https://github.com/IGreatlyDislikeJavascript/bootstrap-tourist/pull/8) for the original change
    - With thanks to [@DancingDad](https://github.com/DancingDad), [@thenewbeat](https://github.com/thenewbeat), [@bardware](https://github.com/bardware) for the fixes/updates
    - You can now change the text displayed for the buttons used in the tour step popups. For this, there is a new object you can pass to the options, called "localization". This option only applies to the default templates. If you specify your own custom template, the localization.buttonTexts option has no effect on the basis that you will make any changes to your own template directly.
        ```js
        var tour = new Tour({
            framework: "bootstrap3",    // or "bootstrap4" depending on your version of bootstrap
            steps: [ ..... ],
            localization: {
                buttonTexts: {
                    prevButton: 'Back',
                    nextButton: 'Go',
                    pauseButton: 'Wait',
                    resumeButton: 'Continue',
                    endTourButton: 'Ok, enough'
                }
            }
        });
        ```
    - You may specify only the labels you want to change. Unspecified labels will remain at their defaults:
        ```js
        var tour = new Tour({
            localization: {
                buttonTexts: {
                    endTourButton: 'Adios muchachos'
                }
            }
        });
        ```
1. Added `showIfUnintendedOrphan`:
    - With thanks to [@diesl](https://github.com/diesl)
    - To show a tour step as an orphan if its element doesn't exist, overriding `onElementUnavailable`
    - If a tour step specifies an element, and the element doesn't exist, `showIfUnintendedOrphan` will show the tour step as an orphan. This ensures your tour step will always be shown.
    - `delayOnElement` takes priority over `showIfUnintendedOrphan`, i.e. if you specify both delayOnElement and showIfUnintendedOrphan, the delay will timeout before the step will be shown as an orphan.
    - This option is available globally and per step.
        ```js
        var tourSteps = [
            {
                element: "#btnSomething",
                showIfUnintendedOrphan: true,
                title: "Always",
                content: "This tour step will always appear, either against element btnSomething if it exists, or as an orphan if it doesn't"
            },
            {
                element: "#btnSomethingElse",
                showIfUnintendedOrphan: true,
                delayOnElement: {
                    delayElement: "element" // use string literal "element" to wait for this step's element, i.e.: #btnSomethingElse
                },
                title: "Always after a delay",
                content: "This tour step will always appear. If element btnSomethingElse doesn't exist, delayOnElement will wait until it exists. If delayOnElement times out, step will show as an orphan"
            },
            {
                element: "#btnDoesntExist",
                showIfUnintendedOrphan: true,
                title: "Always",
                content: "This tour step will always appear",
                onElementUnavailable: function() {
                    console.log("this will never get called as showIfUnintendedOrphan will show step as an orphan");
                }
            },
        ];
        ````
1. Overlay divs and customizable transitions between tour steps:
    - With huge thanks to [@ibastevan](https://github.com/ibastevan), who provided a lot of the code and input to getting this working. Read more here: https://github.com/IGreatlyDislikeJavascript/bootstrap-tourist/pull/24
    - Tourist now uses overlays to highlight tour step elements. A single backdrop div provides the dark/black background, and a highlight div is used to highlight the element of a tour step. Each tour step element is then adjusted by zindex to pop to the top.
    - This option could be considered not exactly simple to understand initially, so please play around with it.
    - A new set of options called backdropOptions has been added globally, and can be overridden per step.
    - `backdropOptions` is an object structured as follows (these are the default options, used if you do not set this option in your tour):
        ```js
        backdropOptions:    {
            highlightOpacity: 0.8,
            highlightColor: '#FFF',
            animation: {
                // can be string of css class or function signature: function(domElement, step) {}
                backdropShow: function(domElement) {
                    domElement.fadeIn();
                },
                backdropHide: function(domElement) {
                    domElement.fadeOut("slow")
                },
                highlightShow: function(domElement, step) {
                    step.fnPositionHighlight();
                    domElement.fadeIn();
                },
                highlightTransition: "tour-highlight-animation",
                highlightHide: function(domElement) {
                    domElement.fadeOut("slow");
                }
            },
        }
        ```
    - `backdropOptions.highlightOpacity`: the alpha value of the div used to highlight the step element. You can control how visible/occluded the element is.
    - `backdropOptions.highlightColor`: the hex color code for the highlight. Normally you will want to use a white highlight (#FFF). However if your step element has a dark or black background, you may want to use a black highlight (#000). Experiment with the best colors for your UX.
    - `backdropOptions.animation`: The options can be either string literals specifying a CSS class, or a function. The application of these features work in exactly the same way for all backdropOptions.animation options. These options apply as per the following:
        - `backdropShow`: when a previously hidden backdrop is shown
        - `backdropHide`: when a previously visible backdrop is hidden
        - `highlightShow`: when step N did not have an element, and step N+1 does have an element
        - `highlightHide`: when step N has an element, and step N+1 does not have an element
        - `highlightTransition`: when both step N and step N+1 have an element, and the highlight is visibly moved from one to the other
    - The highlight is the div that creates the "visibility" effect over a tour step element. This option is how a nice slide/move/transition effect between steps is possible. They also now allow handling of rotated elements etc.
    - If a CSS class name (as a string literal) is provided:
        - If you provide a CSS class, this class will be applied to the specified element at the specified time and removed afterwards. You can use this class to add transition effects. For example, assume you create a class in your CSS that animates an effect as follows:
            ```css
            .my-custom-animation {
                -webkit-transition: all .5s ease-out;
                -moz-transition: all .5s ease-out;
                -ms-transition: all .5s ease-out;
                -o-transition: all .5s ease-out;
                transition: all .5s ease-out;
            }
            ```
        - You can then use this effect every time the background overlay div is shown by specifying it as follows:
            ```js
            var tour = new Tour({
                steps:  [ ..... ],
                backdropOptions: {
                    animation: {
                        backdropShow: "my-custom-animation"
                    },
                }
            });
            ```
        - Now, when moving between step N where step option backdrop == false, and step N + 1 where step option backdrop == true, your class will be used to implement the transition. The class will be added before the backdrop is shown, and removed when the transition is complete. In other words, specifying a CSS class for backdropShow is functionally equivalent to the following code executed when the tour moves between steps:
            ```js
            $(backdropOptions element).addClass("my-custom-animation");
            $(backdropOptions element).show(0, function() {
                $(this).removeClass("my-custom-animation");
            });
            ```
        - Note that the class is removed after the element is visible, therefore only use this option for CSS transitions - do not use it for "persistent" CSS changes.
        - The same approach applies to all the other options, i.e.: if a class is specified for highlightTransition, the class is applied, the highlight div is moved to its new position, and the class is removed.
            - If a function is provided:
                - If you provide a function, your code is 100% responsible for positioning and showing the highlight overlay element.
                - It's more hassle - why would you want to do this? Simply, Tourist can only provide a fairly standard/default set of transitions, especially where
                the highlightTransition option is concerned. If you want your tour to have an especially fancy or featured/custom transition, you can use this option
                to achieve that. You can also work with rotated or transformed elements using this feature.
                - Most of the time you will not need to use this option, and specifying a CSS class will be sufficient. But the option is here if you need it.
                - When specifying a function, it must have the following signature:
                    ```js
                    function(domElement, step) {
                    }
                    ```
                - The domElement parameter provides a jquery object for the element you must manipulate. For example, if you have specified a function for `backdropOptions.animation.highlightShow`, then domElement will be the highlighting div. You must then correctly position and show this div over the step element.
                - The step parameter provides an object with information about the step, to help you decide how to execute the transition. It has the following props, most of which are taken from your tour step. They are re-provided to you for ease, in case you want to create some clever central animation feature etc:
                    ```js
                    step = {
                        element:                // the actual step element, even if the tour uses a function for this step.element option
                        container:              // the container option (string) as specified in the step or globally, to help you decide how to set up the transition
                        backdrop:               // as per step option (bool),
                        preventInteraction:     // as per step option (bool),
                        isOrphan:               // whether is step is actually an orphan, because the element was wrong and showIfUnintendedOrphan == true or for some other reason (bool),
                        orphan:                 // as per step option (bool),
                        showIfUnintendedOrphan: // as per step option (bool),
                        duration:               // as per step option (bool),
                        delay:                  // as per step option (bool),
                        fnPositionHighlight:    // a helper function to position the highlight element
                    };
                    ```
                - The `fnPositionHighlight` option is provided to make it easy for you to automatically position the highlight div on top of the tour step element. The function simply performs the following:
                    ```js
                    function fnPositionHighlight() {
                        $(DOMID_HIGHLIGHT).width(_stepElement.outerWidth())
                            .height(_stepElement.outerHeight())
                            .offset(_stepElement.offset());
                    }
                    ```
                - This allows you to do the following in your customized transition code:
                    ```js
                    function(domElement, step) {
                        // do whatever setup for your custom transition
                        step.fnPositionHighlight();
                    }
                    ```
                - It simply saves some typing.
            - An example of implementing `backdropOptions`:
                - Per step option:
                    ```js
                    var tourSteps = [
                        {
                            element: "#btnSomething",
                            title: "Default",
                            content: "This tour step will use the default Tour.backdropOptions settings"
                        },
                        {
                            orphan: true,
                            title: "Default",
                            content: "This tour step will use the per step transitions as specified"
                            backdropOptions: {
                                    // specify some options and use the defaults for the rest
                                    highlightColor: #000,   // this step element has a black background, so this RGB looks better
                                    animation: {
                                        // use a function to manually hide the highlight - of course this will only apply
                                        // because this step is an orphan == true, and therefore there is no highlight to hide
                                        highlightHide: function(domElement, step) {
                                            domElement.fadeOut("slow")
                                        }
                                    },
                                }
                        },
                    ````
                - Global options:
                        ```js
                        var tour = new Tour({
                            steps:  [ ..... ],
                            backdropOptions: {
                                // default for all Tour steps
                            }
                        });
                        ```

## Policy on NPM, semantic versioning, releases, code structure etc
I'm a self-taught C++/x86 asm coder from the 80's and 90's. I'm not a web developer, I only have basic html, js, jquery knowledge that I pretty much taught myself over a couple of weeks or so. This isn't my full time job, or even my part time job. I inherited the need to fix some stuff in Bootstrap Tour, and I simply published my fixes on github. I never intended to take on maintenance of a product, or become responsible for it in any way. I even said this in the Tour repo when I published my first fixes.

All of that info is to set your expectations. Tourist works, and it's been thoroughly tested, but I keep getting tripped up by the niceties of modern coding. Tourist is offered as a non-minified, simple download-and-drop-in tool for you to use if you want to. I will do my best to follow coding standards, publish to npm when I remember, keep to a coding style, follow semantic versioning and all that stuff that makes it easy for you to use this plugin. However please fully expect that:
1. I will probably fail, so you'll need to tell me what I've done wrong and most importantly how to fix it
1. The github repo will always have the latest in-progress version
1. The github release will always be the latest stable version
1. Anything else is a bonus.

As a side note, Bootstrap Tour was made in coffeescript (which I'd never heard of). So when I started working on Tourist, it was using a codebase without comments, odd transpilation structures and approaches, and much more. So if you're looking at the source and scratching your head as to why something is done in a certain way - yes, welcome to my world :-)

## Contributing
Feel free to contribute with pull requests, bug reports or enhancement suggestions.

## License
Code licensed under the [MIT license](https://opensource.org/licenses/MIT). Documentation licensed under [CC BY 3.0](http://creativecommons.org/licenses/by/3.0/).
