# Annotate Screenshots

An essential feature of Droplr and CloudApp: annotations. We want to edit our screenshots immediately and have those changes beamed right up to the cloud. Let's look at some options.

## 1. Screenshot + annotation software

* **macOS** - Built-in Preview.app [does pretty well](https://www.tweaking4all.com/software/macosx-software/take-screenshot-and-annotate/#BasicAnnotationFunctions).
* **Linux** - I use [Flameshot](https://github.com/lupoDharkael/flameshot). You can find [others](https://wiki.archlinux.org/index.php/Screen_capture#Dedicated_software), too.
* **Windows 10** - You can use the [built-in tool](https://support.microsoft.com/en-us/help/4488540/how-to-take-and-annotate-screenshots-on-windows-10).

If you need to, you can configure your screenshot executeable to automatically launch your favorite graphics editing software. For example, I had this working for a while before I found Flameshot, saved to my `PATH` as `screenshot-annotate`:

```sh
#!/bin/sh
sleep 0.2; scrot -s "$HOME/screenshots/%Y-%m-%d-%H%M%S_\$wx\$h.png" -e 'gimp $f'
```


## 2. Bind to a hotkey

This really varies per platform:

* **macOS** - check [System Preferences](https://support.apple.com/guide/mac-help/create-keyboard-shortcuts-for-apps-mchlp2271/mac).
* **Windows 10** - you [have some built-in options](https://www.laptopmag.com/articles/create-keyboard-shortcuts-windows-10).
* **Linux** - check the docs for your DE or WM, e.g. [Gnome](https://help.gnome.org/users/gnome-help/stable/keyboard-shortcuts-set.html.en), [KDE](https://docs.kde.org/trunk5/en/kde-workspace/kcontrol/khotkeys/index.html), or [i3](https://i3wm.org/docs/userguide.html), [Xmonad](https://github.com/xmonad/xmonad/blob/bb13853929f8f6fc59b526bcc10631e1bac309ad/src/XMonad/Config.hs#L189), etc.

In my case I have Xmonad WM and simply updated my config to launch `flameshot gui` when I press certain keys.


## 3. Watch files

In this step, you want your system to re-upload the file to s3 every time it's created or changes within a specific directory (e.g. `~/screenshots`).

The most cross-platform way:

1. Install [fswatch](https://github.com/emcrisostomo/fswatch/blob/master/INSTALL).
2. Configure it to watch your screenshots dir

```
fswatch --event=Created --event Updated "$HOME/screenshots" | s3-upload --stdin
```
