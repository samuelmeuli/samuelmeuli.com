---
title: Publishing an Electron App on the Mac App Store
date: 2019-04-09
categories: [Tutorials, JavaScript, Electron, macOS]
---

Having your Electron app on the Mac App Store makes it simpler for users to discover and download your app. This guide explains how to get your app listed

<!--more-->

I went through this process when publishing [Mini Diary](https://minidiary.app). `electron-builder`'s docs aren't great and existing tutorials are overly complicated or outdated. This guide aims to provide simple and comprehensive instructions on how to publish an app on the Mac App Store.

**Important:** This tutorial assumes that you have followed _all_ steps outlined in the [previous post on packaging and publishing Electron apps]({{<ref "2019-04-07-packaging-and-publishing-an-electron-app">}}).

### 1. `electron-builder` Configuration

To generate a package for the Mac App Store every time you run your `build` script, extend the `electron-builder` configuration in your `package.json` file by the following settings:

{{<file-name>}}package.json{{</file-name>}}

```json
{
	"build": {
		"mac": {
			"target": ["dmg", "mas", "zip"],
			"electronLanguages": ["en"]
		}
	}
}
```

The `"mas"` key in the `"target"` array configures `electron-builder` to build your app for the Mac App Store (MAS) in addition to the default formats. Specifying `"electronLanguages"` affects which languages are shown on your app's MAS page. If you don't set this parameter, a long list of different languages will be displayed by default. You can find the language keys of the different locales in the [Electron docs](https://electronjs.org/docs/api/locales) (make sure you replace hyphens with underlines).

### 2. App ID

Next, your MAS app needs an App ID. You can generate one in the [Apple Developer Portal](https://developer.apple.com/account/resources/identifiers/list). App IDs are usually of the form `com.yourcompany.yourapp`. This App ID needs to be the same as the one you specified in your `package.json` file under the `build.appId` key.

### 3. Entitlement Files

All apps distributed on the Mac App Store need to be [sandboxed](https://developer.apple.com/app-sandboxing). As the developer of such an app, you need to specify the privileges that your app needs to function, and macOS will prevent it from using any other system functionality.

To specify the privileges required by your app, create the following file in your project's `build` folder:

{{<file-name>}}build/entitlements.mas.plist{{</file-name>}}

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>com.apple.security.app-sandbox</key>
	<true/>
	<!-- Add entitlements here -->
</dict>
</plist>
```

Now, add all entitlements your app needs to this file (see [Apple's docs](https://developer.apple.com/library/archive/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/AboutEntitlements.html) for possible entitlements). The simplest way to do so is using Xcode. For example, if your Electron app needs to be able to read specific files using an open dialog, your entitlements file should look like this:

{{<file-name>}}build/entitlements.mas.plist{{</file-name>}}

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>com.apple.security.app-sandbox</key>
	<true/>
	<key>com.apple.security.files.user-selected.read-only</key>
	<true/>
</dict>
</plist>
```

If you're also distributing your app outside the Mac App Store and intend to sandbox that version as well, create a file named `entitlements.mac.plist` in the `build` directory with the same content as the `entitlements.mas.plist` file.

### 4. Provisioning Profile

Navigate to the ["Profile" section of the Apple Developer Portal](https://developer.apple.com/account/resources/profiles/list). Create two provisioning profiles for your app: A "Mac App Development" and a "Mac App Store" provisioning profile. Download the two files and make sure you remember which one is for development and which one is for production (e.g. by naming them `dev.provisionprofile` and `prod.provisionprofile`).

### 5. Testing

#### Development

To test the MAS build, place `dev.provisionprofile` in your project's root. Extend your `electron-builder` config in `package.json` by the following:

{{<file-name>}}package.json{{</file-name>}}

```json
{
	"build": {
		"mas": {
			"type": "development"
		}
	}
}
```

If you're also sandboxing your non-MAS Mac build, you should also add `"type": "development"` to the `build.mac` config.

Run `yarn build`. You should now be able to open your packaged MAS app (placed in the `dist/mas` directory).

#### Production

If everything is working as expected, you can build your app for production. Simply replace `dev.provisionprofile` with `prod.provisionprofile`, remove the `"type": "development"` lines from your `package.json` again and run the `yarn build` script. Please note that **you will not be able to open the built production app** (it will crash on launch). This is the [expected behavior](https://github.com/electron-userland/electron-builder/issues/1967#issuecomment-323569575).

### 6. Upload to MAS

Go to [App Store Connect](https://appstoreconnect.apple.com) and open "My Apps". Create a new entry for your Mac app.

Open Xcode. From the menu, launch Xcode → Open Developer Tool → Application Loader and follow the steps from there. This tool will upload your app to App Store Connect.

Finally, go back to your app listing in App Store Connect, fill in all information about your app and select the uploaded build. Make sure you explain thoroughly why your app needs the specified entitlements. You can now submit your app for review, which usually takes about a day. Once your app is accepted, you can publish your app on the Mac App Store.

### Sources

- [`electron-builder` docs](https://www.electron.build)
- [Shipping Electron Apps to Mac App Store with Electron Builder](https://medium.com/@jondot/shipping-electron-apps-to-mac-app-store-with-electron-builder-e960d46148ec)
- [Releasing an Electron app on the Mac App Store](https://medium.com/@flaqueEau/releasing-an-electron-app-on-the-mac-app-store-c32dfcd9c2bd)
