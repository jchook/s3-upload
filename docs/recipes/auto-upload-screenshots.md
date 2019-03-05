# Auto-upload screenshots

CloudApp and Droplr let me take a screenshot or a video of the screen, and insta-upload it to the cloud. How can we replicate that behavior?

## Screenshots & Screencast

First we need something to actually take the screenshots.

* **macOS** can use buil-in tools:
  * Use <kbd>Cmd</kbd> + <kbd>Shift</kbd> + <kbd>4</kbd> for partial screenshot
  * Use <kbd>Cmd</kbd> + <kbd>Shift</kbd> + <kbd>3</kbd> for full screenshot
  * Configure a [custom screenshot save location](http://osxdaily.com/2011/01/26/change-the-screenshot-save-file-location-in-mac-os-x/)

* ***nix** can use 3rd party:
  - [`peek`](https://github.com/phw/peek) for screencast WEBM, MP4 and GIF
  - [`flameshot`](https://github.com/lupoDharkael/flameshot) for screenshots (partial or full)


* **Windows 10** can use built-in shortcuts:
  - <kbd>Win</kbd> + <kbd>Print Screen</kbd> for a full screenshot
  - See [this WikiHow](https://www.wikihow.com/Take-a-Screenshot-in-Microsoft-Windows) for more options

## 2. Watching files

Ultimately we want a way to watch the screenshots folder for changes, then call `s3-upload` with the changed file paths.

For a great cross-platform filesystem watcher, try [fswatch](https://github.com/emcrisostomo/fswatch). You can try a command like this on start-up:

```sh
fswatch --event=Created --event=Updated "$HOME/screenshots" | s3-upload --stdin
```

You can specify one or more [event types](https://github.com/emcrisostomo/fswatch/issues/207) to watch:

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

### Alternatives

On Linux you can use [incron](https://inotify.aiken.cz/?section=incron&page=doc&lang=en) to automatically respond to filesystem changes with arbitrary commands.

After installing `incrond`, add this code into [`/var/spool/incron/s3-upload`](https://linux.die.net/man/5/incrontab):

```incrontab
/path/to/screenshots IN_CLOSE_WRITE s3-upload -CN $@/$#
```

Make sure your user owns the file:

```sh
chown "$(whoami)" /var/spool/incron/s3-upload
```


### Annotation

Check out the [guide on annotating screenshots](annotate-screenshots.md).
