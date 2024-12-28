# prefix-patches
Patches, ebuilds, and information for fixing the Gentoo Portage Prefix.

### Platforms tested:
- MacBook Pro Late 2013 (macOS Big Sur, Darwin 20.6, Xcode 13.4.1.)

## build errors
For my first install of the stage3, several build issues arise while trying to install the prefix.

### GCC 14.2:
GCC 14.2 for macOS is broken with newer macOS SDK versions and will prevent certain applications from building.
The Homebrew cask for GCC has a patch written specifically macOS to help fix these issues, but for my purposes, it was much easier to downgrade to GCC 14.1 to get things to work.

### Issues with NSObject and other Apple headers spitting out multiple essays of an error message:
Make sure to use an official Xcode release, on older systems, this isn't possible to download through the AppStore.
You will need to create an Apple developer account with a new/existing Apple ID to download Xcode from their website directly.

https://developer.apple.com/download/all/?q=Xcode

Supported versions will depend on your version of macOS. Check https://xcodereleases.com/ to view which versions are supported for your device.
