klipmenu
========

klipmenu is a simple clipboard manager written in POSIX shell. \
Supports both X server and Wayland.

Greatly influenced by [cdown/clipmenu](https://github.com/cdown/clipmenu/tree/9735907061cc0a69734c887a35a048805539f5dc).

# Usage
## `klipmenud`

You first need to launch the `klipmenud` daemon, it will monitor changes
to the clipboard and cache them in the manager's operational directory.

## `klipmenu`

You may wish to bind a shortcut in your window manager to launch `klipmenu`.

All parameters passed to `klipmenu` after `--` are transparently dispatched
to menu lanuncher. That is, if you usually call e.g. `dmenu` with args to set
colours and other properties, you can invoke `klipmenu` in exactly the same way
to get the same effect, like so:

```
klipmenu -- -i -fn Terminus:size=8 -nb '#002b36' -nf '#839496' -sb '#073642' -sf '#93a1a1'
```

For a full list of environment variables that `klipmenud` can take, please see
`klipmenud --help`.

## `klipctl`

An interface to control `klipmenud` daemon. See `klipctl help` for list of
commands available.

You especially might want to make a wrapper around your password manager
to temporarily disable the clipboard saving, e.g.:

```sh
if [ -x "$(command -v klipmenu)" ]; then
    if [ "$(klipctl status)" = "enabled" ]; then
        trap '{ sleep 1; klipctl enable; } &' EXIT HUP INT
    fi
    if ! klipctl disable 1>&2; then
        >&2 echo 'was unable to disable klipmenu'
    fi
fi

pass "$@"
```

# Dependencies

* POSIX-compilant shell (Dash, Bash, ...)
* some menu program, e.g.:
  * dmenu (the default)
  * rofi
  * fzf
* [wl-clipboard][] (for Wayland)
* [clipnotify][] (for Xorg/XWayland)
* [xclip][] or [xsel][] (for Xorg/XWayland)

If `clipnotify` is not installed, the daemon will attempt to compile
a simplified version of it for its needs. If it fails, the fallback
is to use `sleep 1` as a trigger for checking for clipboard updates.

#

[clipnotify]: https://github.com/cdown/clipnotify
[wl-clipboard]: https://github.com/bugaevc/wl-clipboard
[xclip]: https://github.com/astrand/xclip
[xsel]: http://www.vergenet.net/~conrad/software/xsel/
