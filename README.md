# Data Container

This is a container that will allow you to build "data containers," or squashfs 
binaries that you can mount, unmount, and create all with the same container (and either
use with your own data container base, or on your local machine if you have a FUSE filesystem.

## Getting Started

First, pull the container

```bash
singularity pull --name dinosaur-data shub://vsoch/data-container
```

What does the container do?

```bash
$ singularity apps dinosaur-data
create
mount
unmount
```

Try creating a dinosaur dataset, which is a squashfs filesystem

```bash
$ singularity run --app create dinosaur-data /home/vanessa/Desktop/demo demo.sqfs

Parallel mksquashfs: Using 4 processors
Creating 4.0 filesystem on demo.sqfs, block size 131072.
[===========================================================================================================================|] 2/2 100%
Exportable Squashfs 4.0 filesystem, gzip compressed, data block size 131072
	compressed data, compressed metadata, compressed fragments, compressed xattrs
	duplicates are removed
Filesystem size 3.63 Kbytes (0.00 Mbytes)
	29.28% of uncompressed filesystem size (12.40 Kbytes)
Inode table size 61 bytes (0.06 Kbytes)
	62.24% of uncompressed inode table size (98 bytes)
Directory table size 43 bytes (0.04 Kbytes)
	78.18% of uncompressed directory table size (55 bytes)
Number of duplicate files found 0
Number of inodes 3
Number of files 2
Number of fragments 1
Number of symbolic links  0
Number of device nodes 0
Number of fifo nodes 0
Number of socket nodes 0
Number of directories 1
Number of ids (unique uids + gids) 1
Number of uids 1
	vanessa (1000)
Number of gids 1
	vanessa (1000)
```

Now try mounting it to the container. The container expects you do to this at `/scif/data.sqsh` to 
bind to `/tmp/data`

```bash
cat /etc/fstab
...
/scif/data.sqsh  /tmp/data       squashfs        ro,user,noauto,unhide
```

Here is how to do the mount, interactively with shell. You first bind your squashfs filesystem
to the `/tmp/data.sqsh` location.

```bash
$ singularity shell --bind demo.sqsf:/scif/data.sqsh dinosaur-data
```

Where I'm at (not working yet)

```bash
$ singularity shell --bind demo.sqfs:/scif/data.sqsh dinosaur-data
Singularity: Invoking an interactive shell within container...

Singularity dinosaur-data:~/Documents/Dropbox/Code/srcc/data-container> mount /scif/data
mount: /scif/data.sqsh: failed to setup loop device: Permission denied
Singularity dinosaur-data:~/Documents/Dropbox/Code/srcc/data-container> cat /etc/fstab 
# UNCONFIGURED FSTAB FOR BASE SYSTEM
/scif/data.sqsh  /scif/data       squashfs        ro,user,noauto,unhide,loop
```

```bash
$ singularity shell --bind demo.sqfs:/scif/data.sqsh dinosaur-data
Singularity: Invoking an interactive shell within container...

Singularity dinosaur-data:~/Documents/Dropbox/Code/srcc/data-container> ls /tmp/data
Singularity dinosaur-data:~/Documents/Dropbox/Code/srcc/data-container> squashfuse /scif/data.sqsh /tmp/data
fusermount: mount failed: Operation not permitted
Singularity dinosaur-data:~/Documents/Dropbox/Code/srcc/data-container> fusermount /tmp/data
fusermount: old style mounting not supported
Singularity dinosaur-data:~/Documents/Dropbox/Code/srcc/data-container> mount /tmp/data
mount: /scif/data.sqsh: failed to setup loop device: Permission denied
Singularity dinosaur-data:~/Documents/Dropbox/Code/srcc/data-container> ls -l /scif/data.sqsh 
-rw-r--r-- 1 vanessa vanessa 4096 Jun  1 23:32 /scif/data.sqsh
```

**content below not done, we'd want these commands to work!**
You can always ask for help.

```bash
$ singularity help --app  mount dinosaur-data

Mount a squashfs file to a folder where you have write on you computer!
The folder should NOT exist (but you should have writable to where it would)
as the container will create it for you.


$ singularity run --app mount dinosaur-data demo.sqsf /tmp/data2
$ ls /tmp/data2
```
