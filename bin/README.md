# [`u2c.py`](u2c.py)
* command-line up2k client [(webm)](https://ocv.me/stuff/u2cli.webm)
* file uploads, file-search, autoresume of aborted/broken uploads
* sync local folder to server
* generally faster than browsers
* if something breaks just restart it


# [`partyjournal.py`](partyjournal.py)
produces a chronological list of all uploads by collecting info from up2k databases and the filesystem
* outputs a standalone html file
* optional mapping from IP-addresses to nicknames


# [`partyfuse.py`](partyfuse.py)
* mount a copyparty server as a local filesystem (read-only)
* **supports Windows!** -- expect `194 MiB/s` sequential read
* **supports Linux** -- expect `600 MiB/s` sequential read
* **supports macos** -- expect `85 MiB/s` sequential read

note that copyparty should run with `-ed` to enable dotfiles (hidden otherwise)

and consider using [../docs/rclone.md](../docs/rclone.md) instead; usually a bit faster, especially on windows


## to run this on windows:
* install [winfsp](https://github.com/billziss-gh/winfsp/releases/latest) and [python 3](https://www.python.org/downloads/)
  * [x] add python 3.x to PATH (it asks during install)
* `python -m pip install --user fusepy` (or grab a copy of `fuse.py` from the `connect` page on your copyparty, and keep it in the same folder)
* `python ./partyfuse.py n: http://192.168.1.69:3923/`

10% faster in [msys2](https://www.msys2.org/), 700% faster if debug prints are enabled:
* `pacman -S mingw64/mingw-w64-x86_64-python{,-pip}`
* `/mingw64/bin/python3 -m pip install --user fusepy`
* `/mingw64/bin/python3 ./partyfuse.py [...]`

you could replace winfsp with [dokan](https://github.com/dokan-dev/dokany/releases/latest), let me know if you [figure out how](https://github.com/dokan-dev/dokany/wiki/FUSE)  
(winfsp's sshfs leaks, doesn't look like winfsp itself does, should be fine)



# [`partyfuse2.py`](partyfuse2.py)
* mount a copyparty server as a local filesystem (read-only)
* does the same thing except more correct, `samba` approves
* **supports Linux** -- expect `18 MiB/s` (wait what)
* **supports Macos** -- probably



# [`partyfuse-streaming.py`](partyfuse-streaming.py)
* pretend this doesn't exist



# [`mtag/`](mtag/)
* standalone programs which perform misc. file analysis
* copyparty can Popen programs like these during file indexing to collect additional metadata



# [`dbtool.py`](dbtool.py)
upgrade utility which can show db info and help transfer data between databases, for example when a new version of copyparty is incompatible with the old DB and automatically rebuilds the DB from scratch, but you have some really expensive `-mtp` parsers and want to copy over the tags from the old db

for that example (upgrading to v0.11.20), first launch the new version of copyparty like usual, let it make a backup of the old db and rebuild the new db until the point where it starts running mtp (colored messages as it adds the mtp tags), that's when you hit CTRL-C and patch in the old mtp tags from the old db instead

so assuming you have `-mtp` parsers to provide the tags `key` and `.bpm`:

```
cd /mnt/nas/music/.hist
~/src/copyparty/bin/dbtool.py -ls up2k.db
~/src/copyparty/bin/dbtool.py -src up2k.*.v3 up2k.db -cmp
~/src/copyparty/bin/dbtool.py -src up2k.*.v3 up2k.db -rm-mtp-flag -copy key
~/src/copyparty/bin/dbtool.py -src up2k.*.v3 up2k.db -rm-mtp-flag -copy .bpm -vac
```



# [`prisonparty.sh`](prisonparty.sh)
* run copyparty in a chroot, preventing any accidental file access
* creates bindmounts for /bin, /lib, and so on, see `sysdirs=`
