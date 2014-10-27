# node-mount

Mount/umount devices from node.js

Really works on linux, may work on OS X, and will never work on windows.

##Features
- Asynchrnous/Synchronous mount calls (using libuv)
- Node-like behaviour for async/synch calls and result reporting
- More flag options (noexec)
- Pretty close to C-API

##Installation
This package is not featured in NPM (yet), so you have to add this file in your package.json
file:

**Part of package.json:**
```
{
    "name" : "myproject",
    "version" : "1.0",
    ...
    ...
    "dependencies" : {
        "mount" : "git+ssh://git@github.com:hertzg/node-mount.git"
    }
}
```
## API
This module provides functionality to mount and unmount devices to and from specific targets. Module exports asynchronous and synchronous variants.   

Most of the explanations here are from `mount(2)` and `umount(2)`

* http://linux.die.net/man/2/umount
* http://linux.die.net/man/2/mount

**WARNING: Using synchronous methods may block the process for along time. (ex: Mounting scratched CD/DVD/BD, Network shares.. etc..)**

### mount.mount(devFile, target, fsType, [options], [dataStr], callback)
  
Attaches the file system specified by `devFile` (which is often a device name, but can also be a directory name or a dummy) to the directory specified by `target`.  

**Appropriate privilege is required to mount file systems.**

* `devFile` - `{String}` - Device-File being mounted (located in /dev) a.k.a. `source`.
* `target` - `{String}` - Directory to mount the device to.
* `fsType` - `{String}` - Filesystem identificator (one of /proc/filesystems).
* `options` - `{Array}` - Array containing String options:
    - `bind` - Perform a bind mount, making a file or a directory subtree visible at another point within a file system. Bind mounts may cross file system boundaries and span chroot(2) jails. The `fstype` and `dataStr` arguments are ignored.
    - `readonly` - Mount file system read-only.
    - `remount` - Remount an existing mount. This allows you to change the `options` and `dataStr` of an existing mount without having to unmount and remount the file system. `target` should be the same value specified in the initial `mount()` call; `devFile` and `fsType` are ignored.
    - `noexec` - Do not allow programs to be executed from this file system.
* `dataStr` - The `data` argument is interpreted by the different file systems. Typically it is a string of comma-separated options understood by this file system. 
* `callback` - Function called after the mount operation finishes. Receives only one argument `err`.
    * `err` - Is `null` if mount succeeded or `Error` object similar to ones generated by `fs` module in case of failure.

Values for the `fstype` argument supported by the kernel are listed in `/proc/filesystems` (e.g., "minix", "ext2", "ext3", "jfs", "xfs", "reiserfs", "msdos", "proc", "nfs", "iso9660").

**Mount Tmpfs:**
```javascript
var mount = require("mount");
mount.mount('tmpfs', 'tmpDir', 'tmpfs', function(err) {
	if(err){
        return;
    }
    //Tmpfs mounted successfully
});
```

**Mount DVD:**
```javascript
var mount = require("mount");
mount.mount('/dev/sr0', 'myDir', 'iso9660', function(err) {
    if(err){
        return;
    }

    //
});
```

### mount.umount(target, callback)
Removes the attachment of the (topmost) file system mounted on `target`.

**Appropriate privilege is required to unmount file systems.**

* `target` - `{String}` - Directory to unmount.
* `callback` - Function called after the mount operation finishes. Receives only one argument `err`.
    * `err` -  `null` if umount succeeded or `Error` object similar to ones generated by `fs` module in case of failure.

Values for the `fstype` argument supported by the kernel are listed in `/proc/filesystems` (e.g., "minix", "ext2", "ext3", "jfs", "xfs", "reiserfs", "msdos", "proc", "nfs", "iso9660").

Returns `undefined`

**Umount after successful mount:**
```javascript
var mount = requrie("mount");
mount.umount('myDir', function(err) {
    if(err){
        console.log("Umount went wrong: " + err);
        return;
    }

});
```
### mount.mountSync(devFile, target, fsType, [options], [dataStr])
Synchronous variant of `mount.mount()`

Returns: `true` if mount succeeded or **throws** `Error` object similar to ones generated by `fs` module in case of failure.

### mount.umountSync(target)
Synchronous variant of `mount.umount()`

Returns: `true` if umount succeeded or **throws** `Error` object similar to ones generated by `fs` module in case of failure.

### mount.unmount(target, callback)
This function is **deprecated**; please use `mount.umount()`  instead.

Alias of `mount.umount()`

### mount.unmountSync(target)
This function is **deprecated**; please use `mount.umountSync()` instead.

Alias of `mount.umountSync()`

## Credits
- Directly forked from magicpat`s [repository](https://github.com/magicpat/node-mount)
- magicpat forked from Stackdot`s [repository](https://github.com/stackdot/node-mount)
- Stackdot forked this from Maciej Małecki`s [repository](https://github.com/mmalecki/node-mount)
