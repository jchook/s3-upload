# Annotate Screenshots

The basic princple:

1. Tell your screenshot command to launch your favorite 2D editor, like `gimp`, each time you take a screenshot.
2. Bind it to a hotkey in your window manager or OS.
3. Configure a filesystem event watcher like `fswatch` to respond to changes in the screenshots folder by calling `s3-upload`.

You can also follow the guide to [auto-upload screenshots](auto-upload-screenshots.md) to achieve this functionality.

Here is a quick example of my set-up:

## 1. Screenshot command

I saved this to my PATH, e.g. `$HOME/.local/bin/screenshot-annotate`

```sh
#!/bin/sh
SCREENSHOT_DIR=$HOME/screenshots
mkdir -p $SCREENSHOT_DIR
sleep 0.2; scrot -s "$SCREENSHOT_DIR/%Y-%m-%d-%H%M%S_\$wx\$h.png" -e 'gimp $f'
```

## 2. Bind to a hotkey

This really varies per platform:

* **macOS** - check [System Preferences](https://support.apple.com/guide/mac-help/create-keyboard-shortcuts-for-apps-mchlp2271/mac).
* **Windows 10** - you [have some built-in options](https://www.laptopmag.com/articles/create-keyboard-shortcuts-windows-10).
* **Linux** - check the docs for your DE or WM, e.g. [Gnome](https://help.gnome.org/users/gnome-help/stable/keyboard-shortcuts-set.html.en), [KDE](https://docs.kde.org/trunk5/en/kde-workspace/kcontrol/khotkeys/index.html), or [i3](https://i3wm.org/docs/userguide.html), [Xmonad](https://github.com/xmonad/xmonad/blob/bb13853929f8f6fc59b526bcc10631e1bac309ad/src/XMonad/Config.hs#L189), etc.

In my case I have Xmonad WM and simply updated my config to launch `screenshot-annotate` when I press certain keys.


## 3. Watch files

In this step, you want your system to re-upload the file to s3 every time it's created or changes within a specific directory (e.g. `~/screenshots`).

The most cross-platform way:

1. Install [fswatch](https://github.com/emcrisostomo/fswatch/blob/master/INSTALL).
2. Configure it to watch your screenshots dir

```
fswatch --event=Created --event Updated "$HOME/screenshots" | s3-upload --stdin
```
