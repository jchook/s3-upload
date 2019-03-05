# S3 Upload Command

`s3-upload` allows *nix users to easily upload files to the cloud for free.

I created this to replace [CloudApp](https://www.getcloudapp.com/) for sharing screenshots and videos. Before this, I created a similar command-line tool [specifically for CloudApp](https://github.com/jchook/cloudapp-linux) integration.

## Requirements

- `bash` and `curl` (usually built-in)
- [Amazon S3 account](https://aws.amazon.com/s3/) (free)

## Install

Simply copy the `s3-upload` executable to your `PATH`. For example:

```sh
wget https://raw.githubusercontent.com/jchook/s3-upload/master/s3-upload
chmod +x s3-upload
sudo mv s3-upload /usr/local/bin
```

You can repeat the same process for upgrades to a newer version.

## Usage

To upload a file simply pass it as the argument(s) to the executeable. The URL for the uploaded file will print to standard output. You can use the `--copy` option to copy it to clipboard.

```sh
  s3-upload [options] [--] [<file> | -] ...
```

## Options

Command-line options allow you to override all other configuration at the call site.

```
  -c, --config <file>
    Load a custom configuration file.

  -C, --copy
    Copy the share URL to clipboard.

  -N, --notify
    Notify successful upload via notify-send.

  -n, --name <name>
    Choose custom name(s) for an uploaded file.
    Use multiple times (in order) for multiple files.
    Most useful when piping data into s3-upload via STDIN.

  -k, --key <access-key-id>
    Use a specific IAM access key.

  -p, --secret <secret-key>
    Use a specific IAM access secret. Use empty string to prompt.

  -q, --quiet
    Don't print any information to STDOUT.

  --stdin
    Read a newline-separated file list from STDIN.

  -u, --username <username>
    Use a specific username.

  <file> ...
    Path(s) to the file(s) you want to upload.
    Specify a hyphen (-) to pipe data from stdin.
```

## Config file

You can configure almost all option defaults from the file located in `~/.config/s3-upload`. Use standard bash like so.

```sh
S3_ACCESS_KEY="XXX"
S3_PRIVATE_KEY="YYY"
S3_BUCKET_NAME="ZZZ"
COPY=1
NOTIFY=1
QUIET=
CLIPBOARD="xclip -selection clipboard"
```


## Examples

- Upload a single file
```sh
  s3-upload file.txt
```

- Upload multiple files
```sh
  s3-upload file1.txt file2.mp3
```

- Upload from STDIN
```sh
  curl api.ipify.org | s3-upload --name ip.txt -
```

- Upload a file, notify, and copy the share url to clipboard
```sh
  s3-upload -CN file.png
```

- Upload a file with a custom name / path:
```sh
  s3-upload --name subfolder/custom.png file.png
```



## Recipes

Alone this tool is pretty useful for me, but I found some really great ways to integrate it into my workflow.

### Auto-upload screenshots

On Linux you can use [incron](https://inotify.aiken.cz/?section=incron&page=doc&lang=en) to automatically respond to filesystem changes with arbitrary commands.

After installing `incrond`, add this code into [`/var/spool/incron/s3-upload`](https://linux.die.net/man/5/incrontab) (replace `jchook` with your username):

```incrontab
/path/to/screenshots IN_CLOSE_WRITE s3-upload -CN $@/$#
```

Make sure your user owns the file:

```sh
chown "$(whoami)" /var/spool/incron/s3-upload
```


### Copy share URL to clipboard

A lot of times you want to automatically copy the share URL to the clipboard. You can do this with the `-C` or `--copy` option.

If you want to make this the default behavior, you can add this to your `~/.config/s3-upload` file:

```sh
COPY=1
```
