# Runlinux

cd into a Linux kernel source tree, run one command, and get a running QEMU VM with BusyBox.

Based on: <https://github.com/ivandavidov/minimal>

Tested in Ubuntu 14.04 AMD64, QEMU 2.0.0.

## Install

    sudo apt-get install git qemu
    sudo apt-get build-dep busybox linux-image-$(uname -r)
    mkdir -p ~/bin
    cd ~/bin
    git clone --recursive https://github.com/cirosantilli/runlinux
    echo 'PATH="$PATH:'$(pwd)'/runlinux"' >> ~/.bashrc
    . ~/.bashrc

## Examples

	git clone git://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git
	cd linux
	git checkout v4.2
	runlinux

It takes a while the first time because things have to be built, but later runs will be faster.

Then hack the kernel source to your liking, and run:

	runlinux

again to try it out.

Out-of-tree build with custom configuration:

    export KBUILD_OUTPUT="$(pwd)/../build"
    make menuconfig
	runlinux

If an existing configuration is not found at `KBUILD_OUTPUT`, `make defconfig` is used. If found, it is used and left untouched.

Pass extra options to QEMU:

    runlinux -- -bios ~/path/to/OVMF.fd

This for example uses the [OVMF UEFI X64 r15214](https://sourceforge.net/projects/edk2/files/OVMF/OVMF-X64-r15214.zip/download) instead of the default BIOS.

## Options

## g

TODO this is currently broken: <https://sourceware.org/bugzilla/show_bug.cgi?id=13984>

Debug the kernel on GDB:

	../runlinux/runlinux -g

This will set the `CONFIG_DEBUG_INFO` configuration and rebuild the kernel if necessary.

It runs QEMU on the background of the current shell, and opens GDB there.

You are now ready to debug, e.g.:

    # Has to be hardware breakpoint. TODO why https://bugs.launchpad.net/ubuntu/+source/qemu-kvm/+bug/901944/comments/12
    hbreak start_kernel
    list
    continue
