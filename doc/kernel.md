Kernel for Digabi live
======================================

## TODO
 - add instructions to check GPG signatures (Kernel, patches, ...?)
 - add more specific instructions for module configuration
 - currently patches need to be applied in order: grsecurity -> aufs, or build will fail, check if causes problems / vulnerabilites


## Use digabi-live kernel working directory
    cd custom-packages/linux-image


## Fetch required code
    # Download Linux Kernel <https://www.kernel.org/>
    wget https://www.kernel.org/pub/linux/kernel/v3.x/linux-3.2.48.tar.xz
    # Download grsecurity patch <https://grsecurity.net/>
    wget https://grsecurity.net/stable/grsecurity-2.9.1-3.2.48-201306302051.patch

    # Clone AUFS module from git <http://aufs.sourceforge.net/>
    git clone git://git.code.sf.net/p/aufs/aufs3-standalone aufs3-standalone # alternative url, via http: http://git.code.sf.net/p/aufs/aufs3-standalone
    # Checkout correct branch for our kernel
    cd aufs3-standalone
    git checkout -b aufs3.2 origin/aufs3.2
    cd ..

## Decompress Linux source
    tar -xvJf linux-3.2.48.tar.xz
    cd linux-3.2.48


## Patch Linux source
AUFS patches from git:

    # Apply patches
    patch -p1 < ../aufs3-standalone/aufs3-kbuild.patch
    patch -p1 < ../aufs3-standalone/aufs3-base.patch
    patch -p1 < ../aufs3-standalone/aufs3-proc_map.patch
    patch -p1 < ../aufs3-standalone/aufs3-standalone.patch
    # Copy required files
    cp -R ../aufs3-standalone/{Documentation,fs} .
    cp ../aufs3-standalone/include/linux/aufs_type.h ./include/linux/


Grsecurity from downloaded patch:

    patch -p1 < ../grsecurity-2.9.1-3.2.48-201306302051.patch


## Configure Linux Kernel
### Install required packages
    apt-get install build-essential kernel-package libncurses5-dev


### Create initial config
    make defconfig_i386


### Configure
    make menuconfig


#### Add support for AUFS
    CONFIG_AUFS_FS=m


#### Enable grsecurity


#### Make other modifications
See included kernel config.


## Compile kernel
    fakeroot make-kpkg clean
    fakeroot make-kpkg --initrd kernel_image kernel_headers kernel_source
