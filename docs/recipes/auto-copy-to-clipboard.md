# Auto-copy to clipboard

A lot of times you want to automatically copy the share URL of your file uploads to the system clipboard. You can do this with the `-C` or `--copy` option.

If you want to make this the default behavior, you can add this to your `~/.config/s3-upload` file:

```sh
# Copy URL to clipboard by default
COPY=1

# Use a custom clipboard command (optional)
# This one works on Mac OS X
CLIPBOARD="pbcopy"
```

Everything should work out-of-the-box on Linux and Mac OS.

## Other platforms

For a good cross-platform solution, check out [`clipboard-cli`](https://github.com/sindresorhus/clipboard-cli) on npm. It's supported by default so simply install it globally with npm.

```
npm install --global clipboard-cli
```
