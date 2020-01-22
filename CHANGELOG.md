# Changelog
All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

## [Unreleased]

### Added
 - Resolved issue where [keyboard navigation bypasses reflexOnly] (https://github.com/IGreatlyDislikeJavascript/bootstrap-tourist/issues/45)

## [0.3.2]

### Added
 - backdropOptions.backdropSibling added by [@ibastevan] to solve [position fixed and zindex issues] (https://github.com/IGreatlyDislikeJavascript/bootstrap-tourist/issues/38)

### Changed
 - Updated docs


## [0.3.1]

### Added
 - Added .gitignore
 - Added .npmignore
 - Added package.json
 - Created CHANGELOG

### Changed
 - Updated README

### Removed
 - Removed comments from bootstrap-tourist.js

## [0.3.0]

### Added
 - Added `showIfUnintendedOrphan` to show a tour step as an orphan if its element doesn't exist, overriding `onElementUnavailable`

### Changed
 - New method of backdrop and highlight added, with major effort and code from [@ibastevan](https://github.com/ibastevan):
    - Overlay divs and customizable transitions between tour steps, [read more here](https://github.com/IGreatlyDislikeJavascript/bootstrap-tourist/pull/24)
    - In short, previous versions of Tourist (and original Tour) used 4 divs to bracket the tour step elements. Tourist now uses overlays to produce the same effect. This solves many problems and enables many new features, including customizable transitions between tour steps.

## 0.2.1

### Changed
 - `delayOnElement` option now waits for elements not yet created in the DOM (thanks to [@lukaszmn](https://github.com/lukaszmn))

## [0.2.0]

### Fixed
 - fix bug preventing `element: function(){...}` feature under BS4/popper.js

## [0.0.12-beta]:

### Fixed
 - fixes to the button text change code and better prep for localization (thanks to [@DancingDad](https://github.com/DancingDad), [@thenewbeat](https://github.com/thenewbeat), [@bardware](https://github.com/bardware))
 - fixed css for BS4 progress text to correctly use float-right (thanks to [@macroscian](https://github.com/macroscian), [@thenewbeat](https://github.com/thenewbeat))

## [0.0.10-beta]:

### Added
 - added support for changing button texts (thanks to [@vneri](https://github.com/vneri))
 - added dummy `init()` to support drop-in replacement for Tour (thanks to [@pau1phi11ips](https://github.com/pau1phi11ips))

## 0.0.9-beta:

### Added
 - default params compatibility for IE

### Changed
 - major change to manipulation of BS4 popper.js for orphan steps
 - change to implementation of backdrop

### Removed
 - `smartPlacement` option removed, deprecated

### Fixed
 - auto progress bar was killed in changes 0.7 -> 0.8 due to Bootstrap sanitizer, this is readded

## 0.0.8-beta:

### Changed
 - Improved BS4 support and template switching. Changed options for framework vs template.

### Fixed
 - Fix in 0.0.7-beta didn't work for Bootstrap 4. This release is to ensure fully working popovers in BS4. Issue is that the Bootstrap CDN doesn't actually have the whitelist property, so developing against it is basically useless :(

## 0.0.7-beta:

### Fixed
 - Fix breaking change in Bootstrap 3.4.1, fixes this issue: https://github.com/sorich87/bootstrap-tour/issues/723#issuecomment-471107788. Issue is caused by the BS sanitizer, to avoid this reoccurring the "sanitizeWhitelist:" and "sanitizeFunction:" global options added

## 0.0.6-beta:

### Added
 - Added `onPreviouslyEnded()` callback: https://github.com/sorich87/bootstrap-tour/issues/720
 - Added selector to switch between bootstrap3 and bootstrap4 or custom template, thanks to: https://github.com/sorich87/bootstrap-tour/pull/643

### Fixed
 - Fixed invalid call to debug in `showNextStep()`

## 0.0.5-beta:

### Added
 - Added "unfix" for bootstrap selectpicker to revert zindex after step that includes this plugin

### Changed
 - Improved the background overlay and scroll handling, unnecessary work removed

### Fixed
 - Fixed issue with Bootstrap dialogs. Handling of dialogs is now robust
 - Fixed issue with BootstrapDialog plugin: https://nakupanda.github.io/bootstrap3-dialog/ . See notes below for help.


[unreleased]: https://github.com/olivierlacan/keep-a-changelog/compare/v0.3.1...HEAD
[0.3.1]: https://github.com/IGreatlyDislikeJavascript/bootstrap-tourist/compare/v0.3.0...v0.3.1
[0.3.0]: https://github.com/IGreatlyDislikeJavascript/bootstrap-tourist/compare/v0.2.0...v0.3.0
[0.2.0]: https://github.com/IGreatlyDislikeJavascript/bootstrap-tourist/compare/0.2.0...v0.2.0
[0.0.12-beta]: https://github.com/olivierlacan/keep-a-changelog/compare/v0.10-beta...0.2.0
[0.0.10-beta]: https://github.com/IGreatlyDislikeJavascript/bootstrap-tourist/releases/tag/v0.10-beta
