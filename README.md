This is the early part of the SUSE installation process, before
[YaST](https://en.opensuse.org/Portal:YaST) runs.

See <https://en.opensuse.org/SDB:Linuxrc>.

## Requirements
Building linuxrc will require some additional dependencies.

For example on openSUSE/SUSE distributions run:
```sh
zypper install e2fsprogs-devel hwinfo-devel libblkid-devel libcurl-devel readline-devel
```
## Debugging

### Run on Installed System
Linuxrc can run on installed system. It runs in testmode that make debugging easier.
Parameters are passed as common parametrs like: `linuxrc linemode=0 manual=1 linuxrc.debug=1`

### Useful Shortcuts

* ctrl+c then 'q' - exit linuxrc

* ctrl+c then 'c' - change config

* ctrl+c then 'i' - show info detected by linuxrc

* ctrl+c then 's' - start shell

### Logging
Linuxrc logs to /var/log/linuxrc.log.
To capture a log with maximum verbosity, including the source code location of the logging call,
use these linuxrc params:
`linuxrc.debug=4,trace`.

Linuxrc will also try to log (less verbose) to /dev/tty3. You can redirect this to another location if you need.
For example, on a serial console it might be helpful to log to the current console:
`linuxrc.log=/dev/console`.

## Testing the Installation

A regular SUSE installation DVD gets built via linuxrc.rpm,
then installation-images.rpm,
then a [KIWI image build](https://build.opensuse.org/package/show/openSUSE:Factory/_product:openSUSE-dvd5-dvd-x86_64).

For testing a shortcut is available: mksusecd
([GitHub](https://github.com/openSUSE/mksusecd),
[OBS](https://build.opensuse.org/package/show/system:install:head/mksusecd)).

Use:

```sh
mksusecd --initrd ./linuxrc.rpm --create $OUTPUT_ISO $INPUT
```

or, without an RPM:

```sh
make
mkdir /tmp/initrd
cp linuxrc /tmp/initrd/init
mksusecd --initrd /tmp/initrd --create $OUTPUT_ISO $INPUT
```
Forgetting about the correct shared libraries usually leads to crashes and backtraces printed on the terminal.
When compiling linuxrc on a system different from the one in the input iso, make sure that shared libraries are passed with matching versions; typically you'll need libreadline.so and libhd.so but your mileage may vary. In the following example, linuxrc was compiled on Leap 42.1 to build against Tumbleweed; the versions of the two libraries were not matching on the two versions of openSUSE so the correct versions (including symlinks) were attached with `mksusecd` using the following folder structure:

```sh 
#tree /tmp/initrd
/tmp/initrd
├── init
├── lib64
│   ├── libreadline.so.6 -> libreadline.so.6.2
│   └── libreadline.so.6.2
└── usr
    └── lib64
        ├── libhd.so -> libhd.so.21
        ├── libhd.so.21 -> libhd.so.21.30
        └── libhd.so.21.30

3 directories, 6 files
```

You may also use `mksusecd --micro` in case you only want to test Stage 1
and not a full install.

## openSUSE Development

The package is automatically submitted from the `master` branch to
[system:install:head](https://build.opensuse.org/package/show/system:install:head/linuxrc)
OBS project. From that place it is forwarded to
[openSUSE Factory](https://build.opensuse.org/project/show/openSUSE:Factory).

You can find more information about this workflow in the [linuxrc-devtools
documentation](https://github.com/openSUSE/linuxrc-devtools#opensuse-development).
