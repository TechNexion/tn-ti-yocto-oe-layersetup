## System Requirements

Building an image from source with Yocto necessitates a host system with the following specifications:

- OS: Ubuntu 20.04 or Ubuntu 22.04
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

For Ubuntu 20.04, please run the following:
```sh
sudo apt-get -f -y install gawk wget git-core git-lfs diffstat unzip texinfo gcc-multilib g++-multilib build-essential \
	chrpath socat cpio python python3 python3-pip python3-pexpect python-dev \
	xz-utils debianutils iputils-ping libsdl1.2-dev xterm \
	language-pack-en coreutils texi2html file docbook-utils \
	python-pysqlite2 help2man desktop-file-utils \
	libgl1-mesa-dev libglu1-mesa-dev mercurial autoconf automake \
	groff curl lzop asciidoc u-boot-tools libreoffice-writer \
	sshpass ssh-askpass zip xz-utils kpartx qemu bison flex device-tree-compiler bc rsync \
	cmake libusb-1.0.0-dev libzip-dev libbz2-dev pkg-config libssl-dev manpages-posix-dev bash \
	vim screen sudo libncurses5 zstd liblz4-tool locales
```

For Ubuntu 22.04, please run the following:
```sh
sudo apt-get -f -y install \
	git build-essential diffstat texinfo gawk chrpath socat doxygen \
	dos2unix python3 bison flex libssl-dev u-boot-tools mono-devel \
	mono-complete curl python3-distutils repo pseudo python3-sphinx \
	g++-multilib libc6-dev-i386 jq git-lfs pigz zstd liblz4-tool \
	cpio file lz4 debianutils iputils-ping python3-git python3-jinja2 \
	python3-subunit locales libacl1 unzip gcc python3-pip python3-pexpect \
	xz-utils wget
```

- Set the locale code
```sh
sudo locale-gen en_US.UTF-8
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

| Target                       | Build Output                                                   | Description                             |
| ---------------------------- | -------------------------------------------------------------- | --------------------------------------- |
| tisdk-default-image          | images/&lt;machine&gt;/tisdk-default-image-&lt;machine&gt;.tar.xz          | Target Filesystem                       |
| tisdk-jailhouse-image        | images/&lt;machine&gt;/tisdk-jailhouse-image-&lt;machine&gt;.rootfs.tar.xz | Jailhouse Filesystem                    |
| tisdk-base-image             | images/&lt;machine&gt;/tisdk-base-image-&lt;machine&gt;.tar.xz             | Minimal Target Filesytem                |
| tisdk-thinlinux-image        | images/&lt;machine&gt;/tisdk-thinlinux-image-&lt;machine&gt;.tar.xz   | Minimal Target Filesytem with docker enabled |
| meta-toolchain-arago-tisdk   | sdk/arago-&lt;arago-version&gt;-&lt;architecture&gt;.sh                    | Devkit                                  |

- These are the &lt;machine&gt; in the command

| MACHINE       | Supported boards                          |
| ------------- | ----------------------------------------- |
| axon-am62xx   | TechNexion AXON-AM62XX - GP,HS-FS, HS-SE  |


### Build Steps

-  Fetch Yocto source code:
```sh
git clone --branch scarthgap_6.6.y https://github.com/TechNexion/tn-ti-yocto-oe-layersetup.git

cd tn-ti-yocto-oe-layersetup

./oe-layertool-setup.sh -f configs/technexion/scarthgap_6.6.58-sdk-10.01.10.04-config.txt
```

-  Build Yocto source code:
```sh
cd build
. conf/setenv
MACHINE=<machine> bitbake -k tisdk-default-image
```
