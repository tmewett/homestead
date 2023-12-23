# homestead

The most convenient sandbox for your home directory.

```
Usage: homestead [OPTIONS] [-n NAME] [COMMAND...]

Isolate changes to your home directory.

Runs a command in a sandbox. Everything in the current working directory
is writeable as normal, but other files in your home directory are read-only.

With `-n NAME`, the home directory is writeable, but all changes are stored in
an overlay called NAME. Outside of the homestead, the files are unmodified.
The overlay is created if it doesn't exist.

The homestead overlay is stored in /home/steads/tom.

If COMMAND is not specified, an interactive shell is started.

Options:
  -n NAME       save all writes in the home directory into overlay NAME
  --no-cwd      don't pass the current directory through the sandbox
  -u            unmount & cleanup instead of entering (requires -n)
  -s            isolate more things (PIDs, IPC, cgroup, /proc, /tmp)
  -h, --help    show this help text
```

Example:

```
~/gizmo$ homestead -n gizmo-dev
starting shell in homestead - type ctrl-d or 'exit' to leave
(gizmo-dev) ~/gizmo$ ./install.sh       # I don't know what this command might do!
...
(gizmo-dev) ~/gizmo$ ls ~/.gizmo-files  # Ah, it put stuff in my home
bin/  data/  thing  stuff
(gizmo-dev) ~/gizmo$ exit               # But if I leave the sandbox...
~/gizmo$ ls ~/.gizmo-files              # ...there is nothing left!
ls: cannot access '~/.gizmo-files': No such file or directory
~/gizmo$ ls -a /home/steads/$USER/gizmo-dev  # The files are kept separate
.gizmo-files/
~/gizmo$ homestead -n gizmo-dev         # I can re-enter any time
```

This means you can run unknown code as your user account, without worrying about
it installing, re-configuring, or deleting any files outside of the current
directory. It also means you can cleanly remove any changes, without having to
hunt down what has been added or changed.

Feedback welcome!

## Installation

### Manually

1.  Install Fish, bwrap, and fuse-overlayfs.
    *   Packages `fish bubblewrap fuse-overlayfs` on most distros.
    *   You can use homestead with any shell, Fish just has to be installed.
    *   If fuse-overlayfs isn't packaged, you can download an executable here:
        https://github.com/containers/fuse-overlayfs/releases
1.  Download `homestead` to somewhere in your $PATH.
1.  Create the world-writeable homestead directory as root:

    ```
    mkdir /home/steads
    chmod 777 /home/steads
    ```

Note that homestead can be used as root without fuse-overlayfs.

## Security

Homestead is designed to protect against changes to your home directory from
direct file access. Besides that, the sandbox has a low level of isolation, so
it is possible in theory for processes inside to affect the world outside e.g.
via inter-process communication such as D-Bus. The `-s` option provides more
process isolation, should you require it.

Bubblewrap, the sandbox tool used by homestead, is highly secure, *but the
configuration used is likely insufficient for running truly untrusted code
safely.*
