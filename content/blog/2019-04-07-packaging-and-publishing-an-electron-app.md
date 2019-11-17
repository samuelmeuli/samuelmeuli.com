---
title: Packaging and Publishing an Electron App
date: 2019-04-07
categories: [Tutorials, JavaScript, Electron]
---

Electron simplifies the development of cross-platform desktop apps. Unfortunately, packaging and releasing an Electron app for the different platforms is not a straightforward task. This post aims to guide you through the process.

<!--more-->

### Packaging

[`electron-builder`](https://github.com/electron-userland/electron-builder) is a third-party tool which facilitates the process of packaging an Electron app. The tool handles various tasks required for building a production-ready app, like the inclusion of native dependencies, code signing, upload to a download server, and the inclusion of an auto-updater tool.

Run the following command to add `electron-builder` to your Electron project:

```sh
yarn add --dev electron-builder
```

Next, add the following scripts to your `package.json` file:

<div class="code-file-name"><code>package.json</code></div>

```json
{
  "scripts:": {
    "postinstall": "electron-builder install-app-deps",
    "build": "electron-builder --mac --windows --linux",
    "release": "electron-builder --mac --windows --linux --publish always"
  }
}
```

The `postinstall` script will execute after every `yarn install` and makes sure that native dependencies match your app's version of Electron. The `build` script can be used to package your app for all specified platforms. The `release` script builds the app and publishes it on your download server.

You can also specify configuration parameters for `electron-builder` in your `package.json` file under the `"build"` key. I recommend the following options:

<div class="code-file-name"><code>package.json</code></div>

```json
{
  "name": "your-app",
  "build": {
    "appId": "com.yourcompany.yourapp",
    "productName": "Your App",
    "mac": {
      "category": "public.app-category.lifestyle"
    },
    "dmg": {
      "icon": false
    },
    "linux": {
      "target": ["AppImage"],
      "category": "Office"
    }
  }
}
```

See [here](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/LaunchServicesKeys.html#//apple_ref/doc/uid/TP40009250-SW8) for a list of possible Mac app categories and [here](https://specifications.freedesktop.org/menu-spec/latest/apa.html#main-category-registry) for the available Linux categories.

Finally, create a folder named `build` in your project's root directory and copy your app icon (named `icon.png`) into it. If you don't add this file, the default Electron icon will be used.

You can now run `yarn build` and your app should get packaged for the platforms you specified earlier. You can find the output in the `dist` directory (which you should add to your `.gitignore`).

It's important to know that, by default, `electron-builder` will include all dependencies from your `package.json` file in the app package, whereas development dependencies are ignored. To keep your app size small, you need to specify dependencies as `devDependencies` if your app doesn't use them in production. If you're using a module bundler like Webpack, you should either mark the bundled dependencies as `devDependencies` (to avoid having them in your package twice) or configure `electron-builder` to skip these dependencies (using the `"files"` configuration key).

### Auto Update

`electron-builder` comes with built-in support for `electron-updater`, which checks your download server for new app versions every time the app is launched. If a more recent version exists, it's automatically downloaded in the background and installed after the user closes the app.

To use `electron-updater` in your project, run the following command:

```sh
yarn add electron-updater
```

Add the following code to your main process:

<div class="code-file-name"><code>main.js</code></div>

```js
const autoUpdater = require("electron-updater");

app.on("ready", () => {
  autoUpdater.checkForUpdatesAndNotify();
});
```

If you're using GitHub Releases for distributing your app (explained later in this guide), that's all the configuration you need for the auto updater to work!

### Code Signing

For auto updating to work on macOS, your code needs to be signed:

1. To obtain code signing certificates, you need to be enrolled in the [Apple Developer Program](https://developer.apple.com/programs) (costs annually).
2. Install [Xcode](https://developer.apple.com/xcode) if necessary.
3. Open Xcode → Preferences → Accounts → Manage Certificates. Add one of each macOS-related certificate. Having one of each type allows for distribution both inside and outside the Mac App Store. The certificates are automatically added to your Keychain, where `electron-builder` can detect them. Your code will now automatically be signed whenever you run the `build` script.

[Code signing of Windows apps](https://www.electron.build/code-signing#windows) is optional. If you choose not to sign your app, your users will be presented with a warning when installing your app.

### Release

`electron-builder` supports various platforms for storing your app. By default, GitHub Releases will be used to store the files. To be able to publish on GitHub, [generate a Personal Access Token](https://github.com/settings/tokens) for `electron-builder`.

Whenever you want to publish a new version of your app, do the following:

1. Increase the version number in your `package.json` file. Commit and push this change.
2. Run the `release` script with your GitHub token: `GH_TOKEN=... yarn release`. Your app will be built and the packages will be uploaded to GitHub. A release draft for the new version will be created.
3. Open the GitHub page of your repository and find the new release draft with the download links for your app. Add a changelog and publish the release.

That's it! Your Electron app is now ready to be downloaded from its GitHub page.
