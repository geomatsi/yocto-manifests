# pcDuino upstream (Krogoth) images
Manifest for pcDuino basic Yocto distro based on upstream kernel and u-boot

## Install repo tool

$ curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
$ chmod a+x ~/bin/repo

## Fetch yocto layers

$ mkdir -p /home/builder/project/source  
$ mkdir -p /home/builder/project/build  
$ cd /home/builder/project/source  
$ repo init -u git@github.com:geomatsi/yocto-manifests.git -b master -m sunxi-krogoth.xml  
$ repo sync -c  

## Prepare yocto build environment

$ cd poky  
$ source oe-init-build-env /home/builder/project/build  

## Add the following layers to bblayers.conf:

```
...

BBLAYERS ?= " \
 /home/builder/project/source/poky/meta \
 /home/builder/project/source/poky/meta-poky \
 /home/builder/project/source/meta-openembedded/meta-oe \
 /home/builder/project/source/meta-openembedded/meta-python \
 /home/builder/project/source/meta-openembedded/meta-networking \
 /home/builder/project/source/meta-sunxi \
 "
...

```

## Edit build settings

Replace default machine definition in local.conf:

```
MACHINE = "pcduino"
```

## Building images

$ bitbake core-image-minimal  
$ bitbake core-image-base  

# pcDuino legacy (Fido) images
Manifest for pcDuino basic Yocto distro based on legacy linux-sunxi-v3.4 kernel and legacy u-boot

## Install repo tool

$ curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
$ chmod a+x ~/bin/repo

## Fetch yocto layers

$ mkdir -p /home/builder/project/source  
$ mkdir -p /home/builder/project/build  
$ cd /home/builder/project/source  
$ repo init -u git@github.com:geomatsi/yocto-manifests.git -b master -m sunxi-legacy-fido.xml  
$ repo sync -c  

## Prepare yocto build environment

$ cd poky  
$ source oe-init-build-env /home/builder/project/build  

## Add the following layers to bblayers.conf:

```
...

BBLAYERS ?= " \
 /home/builder/project/source/poky/meta \
 /home/builder/project/source/poky/meta-yocto \
 /home/builder/project/source/meta-openembedded/meta-oe \
 /home/builder/project/source/meta-openembedded/meta-python \
 /home/builder/project/source/meta-openembedded/meta-networking \
 /home/builder/project/source/meta-sunxi \
 "
...

```

## Edit build settings

1. Replace default machine definition in local.conf:

```
MACHINE = "pcduino-lite-wifi"
```

## Building the image

$ bitbake core-image-minimal  
$ bitbake core-image-base  

# BeagleBone (Fido)
Manifest for BeagleBone IoT Yocto distro

## Install repo tool

$ curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
$ chmod a+x ~/bin/repo

## Fetch yocto layers

$ mkdir -p /home/builder/project/source  
$ mkdir -p /home/builder/project/build  
$ cd /home/builder/project/source  
$ repo init -u git@github.com:geomatsi/yocto-manifests.git -b master -m beagle-fido.xml  
$ repo sync -c  

## Prepare yocto build environment

$ cd poky  
$ source oe-init-build-env /home/builder/project/build  

## Add meta-sunxi layer to the build environment

```
--- a/conf/bblayers.conf
+++ b/conf/bblayers.conf
@@ -9,6 +9,7 @@ BBLAYERS ?= " \
    /home/builder/project/source/poky/meta \
    /home/builder/project/source/poky/meta-yocto \
-   /home/builder/project/source/poky/meta-yocto-bsp \
+   /home/builder/project/source/meta-openembedded/meta-oe \
+   /home/builder/project/source/meta-beagle \
+   /home/builder/project/source/meta-iot-simple \
"
BBLAYERS_NON_REMOVABLE ?= " \
    /home/builder/project/source/poky/meta \
```

## Edit build settings

1. Replace default machine definition in local.conf:

```
MACHINE = "beaglebone"
```

## Building the image

$ bitbake core-image-base  
$ bitbake iot-image-base  

# IoT upstream (Krogoth) images
Manifest for pcDuino Yocto distro with IoT software based on upstream kernel and u-boot

## Install repo tool

$ curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
$ chmod a+x ~/bin/repo

## Fetch yocto layers

$ mkdir -p /home/builder/project/source  
$ mkdir -p /home/builder/project/build  
$ cd /home/builder/project/source  
$ repo init -u git@github.com:geomatsi/yocto-manifests.git -b master -m iot-sunxi-krogoth.xml  
$ repo sync -c  

## Prepare yocto build environment

$ cd poky  
$ source oe-init-build-env /home/builder/project/build  

## Add the following layers to bblayers.conf:

```
...

BBLAYERS ?= " \
 /home/builder/project/source/poky/meta \
 /home/builder/project/source/poky/meta-poky \
 /home/builder/project/source/meta-openembedded/meta-oe \
 /home/builder/project/source/meta-openembedded/meta-python \
 /home/builder/project/source/meta-openembedded/meta-networking \
 /home/builder/project/source/meta-nodejs \
 /home/builder/project/source/meta-nodejs-contrib \
 /home/builder/project/source/meta-sunxi \
 /home/builder/project/source/meta-iot-simple \
 "
...

```

## Edit build settings

1. Replace default machine definition in local.conf:

```
MACHINE = "pcduino"
```

## Building images

$ bitbake iot-image-base  
$ bitbake iot-image-web  

