# pcDuino
Manifest for pcDuino IoT Yocto distro

## Fetch yocto layers

$ mkdir -p /home/builder/project/source  
$ mkdir -p /home/builder/project/build  
$ cd /home/builder/project/source  
$ repo init -u git@github.com:geomatsi/iot-manifest.git -b pcduino  

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
+   /home/builder/project/source/meta-sunxi \
+   /home/builder/project/source/meta-iot-simple \
"
BBLAYERS_NON_REMOVABLE ?= " \
    /home/builder/project/source/poky/meta \
```

## Edit machine in build environment

Replace default machine definition in local.conf with MACHINE = "pcduino-lite-wifi"

## Building the image

$ bitbake core-image-base  

# BeagleBone
Manifest for BeagleBone IoT Yocto distro

## Fetch yocto layers

$ mkdir -p /home/builder/project/source  
$ mkdir -p /home/builder/project/build  
$ cd /home/builder/project/source  
$ repo init -u git@github.com:geomatsi/iot-manifest.git -b beaglebone  

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
+   /home/builder/project/source/meta-iot-simple \
"
BBLAYERS_NON_REMOVABLE ?= " \
    /home/builder/project/source/poky/meta \
```

## Edit machine in build environment

Replace default machine definition in local.conf with MACHINE = "beaglebone"

## Building the image

$ bitbake core-image-base  
