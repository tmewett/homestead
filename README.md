# homestead

Rootless sandbox for your home directory.

Homestead starts a shell in a named sandbox. Everything in the current working
directory is writeable as normal, but any changes to other files in your home
directory are stored in an overlay specific to the homestead:

```
~/gizmo$ homestead gizmo-dev
starting shell in homestead 'gizmo-dev' - type ctrl-d or 'exit' to leave
(gizmo-dev) ~/gizmo$ ./install.sh       # I don't know what this command might do!
...
(gizmo-dev) ~/gizmo$ ls ~/.gizmo-files  # Ah, it put stuff in my home
bin/  data/  thing  stuff
(gizmo-dev) ~/gizmo$ exit               # But if I leave the sandbox...
~/gizmo$ ls ~/.gizmo-files              # ...there is nothing left!
ls: cannot access '~/.gizmo-files': No such file or directory
~/gizmo$ ls -a /home/steads/$USER/gizmo-dev  # The files are kept separate
.gizmo-files/
~/gizmo$ homestead gizmo-dev            # I can re-enter any time
```

This means you can run unknown code as your user account, without worrying about
it installing, re-configuring, or deleting any files outside of the current
directory. It also means you can cleanly remove any changes, without having to
hunt down what has been added or changed.

## Installation

### Manually

1.  Install Fish, bwrap, and fuse-overlayfs.
    *   Packages `fish bubblewrap fuse-overlayfs` on most distros.
    *   (You don't need to change shells.)
    *   If fuse-overlayfs isn't packaged, you can download an executable here:
        https://github.com/containers/fuse-overlayfs/releases
1.  Download `homestead` to somewhere in your $PATH.
1.  Create the world-writeable homestead directory as root:

    ```
    mkdir /home/steads
    chmod 777 /home/steads
    ```

See `homestead --help` for usage info.

Note that homestead can be used without fuse-overlayfs; it just requires root.

Feedback welcome!
