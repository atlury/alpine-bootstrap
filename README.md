# alpine-bootstrap

Script to securely bootstrap an Alpine Linux image (based on lxc-alpine
from https://github.com/lxc/lxc).

## Usage

```
Usage: alpine-bootstrap --path=PATH [options...] [PKG...]

PKG  Additional APK package(s) to install into the image.

Options:
   -p PATH, --path=PATH   Place rootfs under PATH
   -a ARCH, --arch=ARCH   The image architecture (e.g. x86, x86_64); defaults
                          to the host arch.
   -d, --debug            Run this script in a debug mode (set -x and wget w/o -q).
   -F, --flush-cache      Remove cached files before build.
   -m URL --mirror=URL    The Alpine mirror to use; defaults to random mirror.
   -r VER, --release=VER  The Alpine release branch to install; default is the
                          latest stable.

Environment variables:
   APK             The apk-tools binary to use when building rootfs. If not set
                   or not executable and apk is not on PATH, then the script
                   will download the latest apk-tools-static.
   APK_KEYS_DIR    Path to directory with GPG keys for APK. If not set and
                   /etc/apk/keys does not contain alpine keys, then the script
                   will download the keys from http://alpinelinux.org/keys.
```

## Example

```
mkdir -p /tmp/alpine/rootfs
qemu-img create -f raw /tmp/alpine/alpine.img 512m
sudo parted --script /tmp/alpine/alpine.img -- mklabel msdos mkpart p 1 -1 set 1 boot on
sudo kpartx -av /tmp/alpine/alpine.img
sudo mkfs.ext4 /dev/mapper/loop0p1
sudo mount /dev/mapper/loop0p1 /tmp/alpine/rootfs/

sudo ./alpine-bootstrap --path /tmp/alpine/rootfs/

sudo umount /tmp/alpine/rootfs
sudo kpartx -vd /dev/loop0

kvm /tmp/alpine/alpine.img -m 1024
```
