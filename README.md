# live-hasher

Hashfile maker for directories, that can keep watching the directories for changed added and changes files (by size/mtime). 

The use case this was for is dealing sensibly with dataset files being copied in during data collection, in that these files might be appended to or otherwise change content.

Also stops watching the directory after a time.


### Limitations
                                                                                                               
The main caveat is that when you stop and re-run, it can't really do a size or mtime check,
because the hashfile (our only information store between runs) doesn't contain these.

There's a "re-hash files with mtime younger than X" argument (default 5min) to help there, 
but it makes the assumption that older files never change, and that mtime means local age (e.g. not true in rsync).
so you should be sure that makes sense for your use.


### Notes:
* One thread per directory argument, which can help speed when they are on different mounts.

* code can read/check MD5 or SHA1. Writing is currently only SHA1

* checker code is basically just equivalent to md5sum -c / sha1sum -c

* tries to avoid losing work with an ill-placed Ctrl-C:
  * new hashfile is saved to a temporary file, then moved into place
  * hashfile is written every-so-many files (default 500) and every-so-many bytes bytes (default 1GB)

* prints/stores relative paths  (internally it's absolute)

* First-ish version, not thoroughly tested, don't rely on it for your dog's / company's safety
