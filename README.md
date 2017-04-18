# Preliminary setup

## repo tool
Install repo tool
```bash
$ curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
$ chmod a+x ~/bin/repo
```

# Upstream images: Yocto v2.2 Morty
Morty upstream images are based on upstream kernel v4.9 and u-boot v2016.11.

## Fetch Yocto layers

```bash
$ mkdir -p /home/builder/project/source
$ mkdir -p /home/builder/project/build
$ cd /home/builder/project/source
$ repo init -u git@github.com:geomatsi/yocto-manifests.git -b master -m iot-sunxi-morty.xml
$ repo sync -c
```

## Prepare Yocto build environment

```bash
$ cd poky
$ source oe-init-build-env /home/builder/project/build
```

Yocto configuration files will be stored in /home/builder/project/build/conf
directory.

## Edit build settings

### bblayers.conf
Add the following layers to bblayers.conf:

```asciidoc
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

### local.conf

1. Machine definition

   For pcDuino-Lite-WiFi board replace default machine definition with the following:

   ```asciidoc
   MACHINE = "pcduino"
   ```

   For Orange-Pi One board replace default machine definition with the following:

   ```asciidoc
   MACHINE = "orange-pi-one"
   ```

2. Init system

   By default sysvinit init system is enabled on all the images. In order to build
   image with systemd add the following lines at the bottom of local.conf:
   
   ```asciidoc
   DISTRO_FEATURES_append = " systemd"
   DISTRO_FEATURES_BACKFILL_CONSIDERED += "sysvinit"
   VIRTUAL-RUNTIME_init_manager = "systemd"
   VIRTUAL-RUNTIME_initscripts = "systemd-compat-units"
   ```

3. Parallel build

   Add the following lines at the bottom of local.conf in order to explicitely
   specify parallel build configuration:
   
   ```asciidoc
   PARALLEL_MAKE = "-j 2"
   BB_NUMBER_THREADS = "2"
   ```

## Building images

```bash
$ bitbake core-image-minimal
$ bitbake core-image-base
$ bitbake iot-image-base
$ bitbake iot-image-web
```

# Legacy images: Allwinner BSP and Yocto v1.8 Fido
Legacy images are based on Allwinner BSP which includes patched kernel v3.4
kernel and u-boot v2014.04.

## Fetch yocto layers

```bash
$ mkdir -p /home/builder/project/source
$ mkdir -p /home/builder/project/build
$ cd /home/builder/project/source

# sunxi boards
$ repo init -u git@github.com:geomatsi/yocto-manifests.git -b master -m sunxi-legacy-fido.xml

# BeagleBone board
$ repo init -u git@github.com:geomatsi/yocto-manifests.git -b master -m beagle-legacy-fido.xml  

$ repo sync -c
```

## Prepare yocto build environment

```bash
$ cd poky
$ source oe-init-build-env /home/builder/project/build
```

## Edit build settings

### bblayers.conf
For sunxi boards add the following layers to bblayers.conf:

```asciidoc
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

For BeagleBone board add the following layers to bblayers.conf:

```asciidoc
...

BBLAYERS ?= " \
 /home/builder/project/source/poky/meta \
 /home/builder/project/source/poky/meta-yocto \
 /home/builder/project/source/meta-openembedded/meta-oe \
 /home/builder/project/source/meta-openembedded/meta-python \
 /home/builder/project/source/meta-openembedded/meta-networking \
 /home/builder/project/source/meta-beagle \
 "
...
```

### local.conf

Replace default machine definition in local.conf.

For pcDuino-Lite-WiFi board:
```asciidoc
MACHINE = "pcduino-lite-wifi"
```

For BeagleBone board:
```asciidoc
MACHINE = "beaglebone"
```

## Building the image

```bash
$ bitbake core-image-minimal
$ bitbake core-image-base
```
