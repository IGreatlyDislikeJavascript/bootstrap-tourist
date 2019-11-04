# Changelog
All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
 - Added .gitignore, .npmignore, and package.json
 - Created CHANGELOG

## Changed
 - Updated README
 - starting to follow semantic versioning

## [0.3.0]

### Added
 - Added `showIfUnintendedOrphan` to show a tour step as an orphan if its element doesn't exist, overriding `onElementUnavailable`

### Changed
 - new method of backdrop and highlight added, with major effort and code from @ibastevan:
    - Overlay divs and customizable transitions between tour steps
    - https://github.com/IGreatlyDislikeJavascript/bootstrap-tourist/pull/24
    - In short, previous versions of Tourist (and original Tour) used 4 divs to bracket the tour step elements. Tourist now uses overlays to produce the same effect. This solves many problems and enables many new features, including customizable transitions between tour steps.

## [0.2.1]

### Changed
 - `delayOnElement` option now waits for elements not yet created in the DOM (thanks to @lukaszmn)

## [0.2.0]

### Fixed
 - fix bug preventing `element: function(){...}` feature under BS4/popper.js

## 0.0.12-beta:

### Fixed
 - fixes to the button text change code and better prep for localization (thanks to @DancingDad, @thenewbeat, @bardware)
 - fixed css for BS4 progress text to correctly use float-right (thanks to @macroscian, @thenewbeat)

## [0.10-beta]:

### Added
 - added support for changing button texts (thanks to @vneri)
 - added dummy `init()` to support drop-in replacement for Tour (thanks to @pau1phi11ips)

## 0.0.9-beta:

### Added
 - default params compatibility for IE

### Changed
 - major change to manipulation of BS4 popper.js for orphan steps
 - change to implementation of backdrop

### Fixed
 - auto progress bar was killed in changes 0.7 -> 0.8 due to Bootstrap sanitizer, this is readded

### Removed
 - `smartPlacement` option removed, deprecated

## 0.0.8-beta:
### Fixed
 - Fix in 0.0.7-beta didn't work for Bootstrap 4. This release is to ensure fully working popovers in BS4. Issue is that the Bootstrap CDN doesn't actually have the whitelist property, so developing against it is basically useless :(

### Changed
 - Improved BS4 support and template switching. Changed options for framework vs template.

## 0.0.7-beta:

### Fixed
 - Fix breaking change in Bootstrap 3.4.1, fixes this issue: https://github.com/sorich87/bootstrap-tour/issues/723#issuecomment-471107788
    Issue is caused by the BS sanitizer, to avoid this reoccurring the "sanitizeWhitelist:" and "sanitizeFunction:" global options added

## 0.0.6-beta:

### Added
 - Added `onPreviouslyEnded()` callback: https://github.com/sorich87/bootstrap-tour/issues/720
 - Added selector to switch between bootstrap3 and bootstrap4 or custom template, thanks to: https://github.com/sorich87/bootstrap-tour/pull/643

### Fixed
 - Fixed invalid call to debug in `showNextStep()`

## 0.0.5-beta:

### Added
 - Added "unfix" for bootstrap selectpicker to revert zindex after step that includes this plugin

### Fixed
 - Fixed issue with Bootstrap dialogs. Handling of dialogs is now robust
 - Fixed issue with BootstrapDialog plugin: https://nakupanda.github.io/bootstrap3-dialog/ . See notes below for help.

### Changed
 - Improved the background overlay and scroll handling, unnecessary work removed
