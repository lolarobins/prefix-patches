# prefix-patches
Patches, ebuilds, and information for fixing the Gentoo Portage Prefix for macOS.

### Platforms tested:
- MacBook Pro Late 2013 (macOS Big Sur, Darwin 20.6, Xcode 13)

## prefix build errors
For my first install of the stage-3 using the [Gentoo Prefix Bootstrap](https://wiki.gentoo.org/wiki/Project:Prefix/Bootstrap), several build issues arose while trying to install the prefix.

### GCC 14.2:
GCC 14.2 for macOS is broken with newer macOS SDK versions and will prevent certain applications from building.
The Homebrew cask for GCC has a patch written specifically macOS to help fix these issues, but for my purposes, it was much easier to downgrade to GCC 14.1 to get things to work.

### Issues with CFUserNotification.h and other Apple headers spitting out multiple essays of an error message:

In order to make changes, you will need to clone the MacOSX.sdk in the Gentoo prefix root directory from the existing symlink. This can be done by doing
`cp -LR MacOSX.sdk .MacOSX.sdk && rm MacOSX.sdk && mv .MacOSX.sdk MacOSX.sdk`

In CFUserNotification.h from lines 126-128, the `API_UNAVAILABLE` attributes are placed in a non-standard order that isn't supported by GCC. This can be done by replacing these lines.
```
Old:
126 | CF_INLINE CFOptionFlags CFUserNotificationCheckBoxChecked(CFIndex i) API_AVAILABLE(macos(10.0)) API_UNAVAILABLE(ios, watchos, tvos) {return ((CFOptionFlags)(1UL << (8 + i)));}
127 | CF_INLINE CFOptionFlags CFUserNotificationSecureTextField(CFIndex i) API_AVAILABLE(macos(10.0)) API_UNAVAILABLE(ios, watchos, tvos) {return ((CFOptionFlags)(1UL << (16 + i)));}
128 | CF_INLINE CFOptionFlags CFUserNotificationPopUpSelection(CFIndex n) API_AVAILABLE(macos(10.0)) API_UNAVAILABLE(ios, watchos, tvos) {return ((CFOptionFlags)(n << 24));}

New:
126 | CF_INLINE API_AVAILABLE(macos(10.0)) API_UNAVAILABLE(ios, watchos, tvos) CFOptionFlags CFUserNotificationCheckBoxChecked(CFIndex i) {return ((CFOptionFlags)(1UL << (8 + i)));}
127 | CF_INLINE API_AVAILABLE(macos(10.0)) API_UNAVAILABLE(ios, watchos, tvos) CFOptionFlags CFUserNotificationSecureTextField(CFIndex i) {return ((CFOptionFlags)(1UL << (16 + i)));}
128 | CF_INLINE API_AVAILABLE(macos(10.0)) API_UNAVAILABLE(ios, watchos, tvos) CFOptionFlags CFUserNotificationPopUpSelection(CFIndex n) {return ((CFOptionFlags)(n << 24));}
```

EDIT idk if this is correct, more testing needed:

Make sure to use an official Xcode release, on older systems, this isn't possible to download through the AppStore.
You will need to create an Apple developer account with a new/existing Apple ID to download Xcode from their website directly.

https://developer.apple.com/download/all/?q=Xcode

Supported versions will depend on your version of macOS. Check https://xcodereleases.com/ to view which versions are supported for your device.

Once downloaded, running `ln -sfn <Xcode.app path>/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk MacOSX.sdk` in the Gentoo prefix's root directory will allow you to swap to the newly downloaded Xcode SDK.
