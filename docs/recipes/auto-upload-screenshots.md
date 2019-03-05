# Auto-upload screenshots

One of the key features I enjoy about CloudApp and Droplr is the ability to take a screenshot or a video of the screen.

Many systems like macOS have built-in tools for screencast + screenshot. On my Linux system, I use:

- [`peek`](https://github.com/phw/peek) for screencast WEBM, MP4 and GIF
- [`scrot`](http://manpages.ubuntu.com/manpages/cosmic/en/man1/scrot.1.html) for screenshots

Ultimately we need a way to watch the filesystem for changes, then call `s3-upload` with the changed file paths.

## Cross-platform

For a great cross-platform filesystem watcher, try [fswatch](https://github.com/emcrisostomo/fswatch). You can try a command like this on start-up:

```sh
fswatch --event=Created "$HOME/screenshots" | s3-upload --stdin
```

, I use . Peek remembers the destination folder you choose for next time.

They make it hard to find the available event types:

```
NoOp
PlatformSpecific
Created
Updated
Removed
Renamed
OwnerModified
AttributeModified
MovedFrom
MovedTo
IsFile
IsDir
IsSymLink
Link
Overflow
```

## Linux

On Linux you can use [incron](https://inotify.aiken.cz/?section=incron&page=doc&lang=en) to automatically respond to filesystem changes with arbitrary commands.

After installing `incrond`, add this code into [`/var/spool/incron/s3-upload`](https://linux.die.net/man/5/incrontab):

```incrontab
/path/to/screenshots IN_CLOSE_WRITE s3-upload -CN $@/$#
```

Make sure your user owns the file:

```sh
chown "$(whoami)" /var/spool/incron/s3-upload
```

If you don't have a screenshot app, I use [`scrot`](https://www.tecmint.com/take-screenshots-in-linux-using-scrot/).

I made an executable called `screenshot-select` with this content:

```sh
#!/bin/sh
SCREENSHOT_DIR="$HOME/screenshots"
mkdir -p $SCREENSHOT_DIR
sleep 0.2; scrot -s "$SCREENSHOT_DIR/%Y-%m-%d-%H%M%S_\$wx\$h.png" -e "xdg-open \$f"
```

Then I bound then executable to a hotkey using my window manager.

You can bind additonal keys and executables for other similar results.

### Full screenshot

Create a `screenshot` executable, just change `scrot -s` to `scrot -m`.

```sh
#!/bin/sh
sleep 0.2; scrot -m "$HOME/screenshots/%Y-%m-%d-%H%M%S_\$wx\$h.png" -e "xdg-open \$f"
```

### Annotation

You can  annotate the screenshots, you can change `xdg-open` to your favorite editor such as `gimp`. I have `screenshot-annotate`:

```sh
#!/bin/sh
sleep 0.2; scrot -s "$HOME/screenshots/%Y-%m-%d-%H%M%S_\$wx\$h.png" -e "gimp \$f"
```

You can assign this to a separate hotkey to have quick access to annotating files. If you set-up your file watcher properly, e.g. with `fswatch --event=Updated`, then it should re-upload the annotated version automatically.

On other platforms, you can apply this same principle with alternative software.
