## System Requirements

Building an image from source with Yocto necessitates a host system with the following specifications:

- OS: Ubuntu 20.04
- CPU: 4 Core
- RAM: 8/16GB RAM (more is preferable)
- Storage: 200 GB
- SWAP space: 16GB
    - **If less memory is available, additional swap space may be necessary. Insufficient memory can lead to sluggish builds and sporadic build errors.**
- Network
    - The host must be connected to a network with Internet access to download all necessary source code and tools.

## Set up build environment on host PC

### Install required packages

- Update the host package list
```sh
sudo apt-get update
```

- Install required packages
```sh
sudo apt-get install gawk wget git-core git-lfs diffstat unzip texinfo gcc-multilib g++-multilib build-essential \
	chrpath socat cpio python python3 python3-pip python3-pexpect python-dev \
	xz-utils debianutils iputils-ping libsdl1.2-dev xterm \
	language-pack-en coreutils texi2html file docbook-utils \
	python-pysqlite2 help2man desktop-file-utils \
	libgl1-mesa-dev libglu1-mesa-dev mercurial autoconf automake \
	groff curl lzop asciidoc u-boot-tools libreoffice-writer \
	sshpass ssh-askpass zip xz-utils kpartx qemu bison flex device-tree-compiler bc rsync \
	cmake libusb-1.0.0-dev libzip-dev libbz2-dev pkg-config libssl-dev manpages-posix-dev bash \
	vim screen sudo libncurses5 zstd liblz4-tool
```

### Switching to bash environment
TI Yocto utilizes `oe-layersetup` tool and it requires to run under `bash` environment.

By default, Ubuntu uses `dash` instead of `bash`. Please switch the default shell to `bash` using the command below:
```sh
echo "dash dash/sh boolean false" | debconf-set-selections
DEBIAN_FRONTEND=noninteractive dpkg-reconfigure dash
```

## Build OS image for TechNexion target platform
- Common target images

| Target                       | Build Output                                            | Description                                  |
| ---------------------------- | ------------------------------------------------------- | -------------------------------------------- |
| tisdk-core-bundle            | images/<machine>/tisdk-core-bundle-<machine>.tar.xz     | Full SDK                                     |
| tisdk-default-image          | images/<machine>/tisdk-default-image-<machine>.tar.xz   | Target Filesystem                            |
| tisdk-edgeai-image           | images/<machine>/tisdk-edgeai-image-<machine>.tar.xz    | Target EdgeAI Filesystem                     |
| tisdk-adas-image             | images/<machine>/tisdk-adas-image-<machine>.tar.xz      | Target Adas Filesystem                       |
| tisdk-base-image             | images/<machine>/tisdk-base-image-<machine>.tar.xz      | Minimal Target Filesytem                     |
| tisdk-thinlinux-image        | images/<machine>/tisdk-thinlinux-image-<machine>.tar.xz | Minimal Target Filesytem with docker enabled |
| meta-toolchain-arago-tisdk   | sdk/arago-<arago-version>-<architecture>.sh             | Linux glibc toolchain                        |
| mc:k3r5:meta-toolchain-arago | sdk/arago-<arago-version>-<architecture>.sh             | K3R5 baremetal toolchain                     |

### For ROVY-4VM

-  Fetch Yocto source code:
```sh
git clone --branch kirkstone_6.1.y https://github.com/TechNexion/tn-ti-yocto-oe-layersetup.git

cd tn-ti-yocto-oe-layersetup

./oe-layertool-setup.sh -f configs/technexion/tda4vm_kirkstone_6.1.46-sdk-analytics-09.01.00-config.txt
```

-  Build Yocto source code:
```sh
cd build
echo 'ARAGO_BRAND = "edgeai"' >> conf/local.conf
. conf/setenv
MACHINE=rovy-4vm bitbake -k tisdk-edgeai-image
```


