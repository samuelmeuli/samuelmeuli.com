---
title: Removing Dead Keys on macOS
date: 2019-11-17
categories: [Tutorials, macOS]
---

A tutorial on re-mapping dead keys on macOS keyboard layouts

<!--more-->

Depending on the macOS keyboard layout you're using, you might have "dead keys". A dead key represents a symbol which isn't a full character, and therefore isn't immediately inserted into the text when typed. Examples of dead keys are `^`, `` ` `` and `~`. For instance, when typing `` ` `` followed by `a`, macOS will insert `à` instead.

For programmers, this behavior is often undesirable. For example, the tilde (`~`) is a useful shortcut for the `$HOME` shell variable, and the backtick (`` ` ``) is used for template literals in JavaScript. Therefore, if you're not using the symbols in your language, it might be preferable if these characters were inserted immediately when typed.

Fortunately, reconfiguring your keyboard layout to remove these dead keys is relatively easy. The [Ukelele](https://software.sil.org/ukelele) app can help you with this task:

1. Download and install Ukelele from [its website](https://software.sil.org/ukelele/#downloads) or using `brew cask install ukelele`.
2. Open the Ukelele app.
3. In the application menu, select `File` → `New From Current Input Source`. This will detect your keyboard layout and create a copy which you can modify.
4. In the main window, Ukelele displays your layout with the dead keys highlighted in red. Right-click each of your dead keys, select the corresponding modifier key if necessary (e.g. the shift or option key) and enter the key's value at the bottom of the dialog. This will change the key's behavior to insert the symbol right away.
5. Once you've re-mapped all dead keys, save your keyboard layout and copy it to the `~/Library/Keyboard Layouts` directory.
6. Reboot your Mac.
7. Open the System Preferences and navigate to `Keyboard` → `Input Sources` → `+`. Your custom keyboard layout can be found under the language it's based on. You can now add your new keyboard layout and activate it from the menu bar.
