# Preliminary setup

## repo tool
Install repo tool
```bash
$ curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
$ chmod a+x ~/bin/repo
```

## Node.js specific dependencies
See details at https://github.com/imyller/meta-nodejs for nodejs prerequisites.
In brief, the following packages should be installed in order to build
images for 32-bit ARM on x86_64 Ubuntu or Debian host:

```bash
$ sudo dpkg --add-architecture i386
$ sudo apt-get update
$ sudo apt-get install g++-multilib libssl-dev:i386 libcrypto++-dev:i386 zlib1g-dev:i386
```

# Octoprint images based on meta-maker

## Upstream Octoprint images: Yocto v2.4 Rocko
Yocto Rocko images are based on Octoprint 1.4.x development branch.
Yocto Pyro images are based on Octoprint 1.3.x stable branch.

### Fetch Yocto layers

Use octoprint-sunxi-rocko.xml manifest:
```bash
$ mkdir -p /home/builder/project/source
$ mkdir -p /home/builder/project/build
$ cd /home/builder/project/source
$ repo init -u https://github.com/geomatsi/yocto-manifests.git -b master -m octoprint-sunxi-rocko.xml
$ repo sync -c
```

### Prepare Yocto build environment

```bash
$ cd poky
$ source oe-init-build-env /home/builder/project/build
```

Yocto configuration files will be stored in /home/builder/project/build/conf
directory.

### Edit build settings

#### bblayers.conf
Add the following layers to bblayers.conf:

```asciidoc
...

BBLAYERS ?= " \
 /home/builder/project/source/poky/meta \
 /home/builder/project/source/poky/meta-poky \
 /home/builder/project/source/openembedded-core/meta \
 /home/builder/project/source/meta-openembedded/meta-oe \
 /home/builder/project/source/meta-openembedded/meta-perl \
 /home/builder/project/source/meta-openembedded/meta-python \
 /home/builder/project/source/meta-openembedded/meta-webserver \
 /home/builder/project/source/meta-openembedded/meta-networking \
 /home/builder/project/source/meta-sunxi \
 /home/builder/project/source/meta-sunxi-contrib \
 /home/builder/project/source/meta-maker \
 /home/builder/project/source/meta-octoprint-images \
 "
...

```

#### local.conf

1. Machine definition
   For Orange-Pi PC Plus board replace default machine definition with the following:

   ```asciidoc
   MACHINE = "orange-pi-pc-plus"
   ```

   For Orange-Pi Zero board replace default machine definition with the following:

   ```asciidoc
   MACHINE = "orange-pi-zero"
   ```

2. Package manager

   Octoprint has quite a few dependencies including various python packages.
   To simplify troubleshooting it makes sense to use rpm or deb package managers
   which provide more useful error reporting on do_rootfs failures.

   ```asciidoc
   PACKAGE_CLASSES = "package_rpm"
   ```

3. Licenses

   Enable non-free licenses for FFmpeg and Cura adding the following
   line at the bottom of local.conf:

   ```asciidoc
   LICENSE_FLAGS_WHITELIST = "commercial"
   ```

4. Octoprint python dependencies

   Layer meta-maker enables custom providers for several python packages.
   Those custom providers are needed to satisfy Octoprint requirements
   for its python dependencies. To help Yocto to choose the right ones
   add the following lines at the bottom of local.conf:

   ```asciidoc
   PREFERRED_RPROVIDER_python-werkzeug = "python-werkzeug11"
   PREFERRED_RPROVIDER_python-jinja2 = "python-jinja2.8"
   ```

5. WiFi support on Orange Pi boards

   Install WiFi kernel modules to image adding the following lines
   at the bottom of local.conf:

   For Orange-Pi PC Plus board:
   
   ```asciidoc
   IMAGE_INSTALL_append = " rtl8189fs-mod "
   ```

   For Orange-Pi Zero board:
   
   ```asciidoc
   IMAGE_INSTALL_append = " xradio-mod "
   ```
 
# IoT images: MQTT/Node/nRF24

## Upstream IoT images: Yocto v2.2 Morty and v2.5 Sumo
Yocto Morty images are based on upstream kernel v4.9 and u-boot v2016.11.
Note that nodejs is built using Morty branch of meta-nodejs layer
in both Morty and Sumo images.

### Fetch Yocto layers

```bash
$ mkdir -p /home/builder/project/source
$ mkdir -p /home/builder/project/build
$ cd /home/builder/project/source
$ repo init -u https://github.com/geomatsi/yocto-manifests.git -b master -m iot-sunxi-sumo.xml
$ repo sync -c
```

### Prepare Yocto build environment

```bash
$ cd poky
$ source oe-init-build-env /home/builder/project/build
```

Yocto configuration files will be stored in /home/builder/project/build/conf
directory.

### Edit build settings

#### bblayers.conf
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
 /home/builder/project/source/meta-sunxi-contrib \
 /home/builder/project/source/meta-iot-simple \
 /home/builder/project/source/meta-hamster \
 "
...

```

#### local.conf

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

### Building images

```bash
$ bitbake core-image-minimal
$ bitbake core-image-base
$ bitbake iot-image-base
$ bitbake iot-image-web
```

## Legacy IoT images: Allwinner BSP and Yocto v1.8 Fido
Legacy images are based on Allwinner BSP which includes patched kernel v3.4
kernel and u-boot v2014.04.

### Fetch yocto layers

```bash
$ mkdir -p /home/builder/project/source
$ mkdir -p /home/builder/project/build
$ cd /home/builder/project/source

# sunxi boards
$ repo init -u https://github.com/geomatsi/yocto-manifests.git -b master -m sunxi-legacy-fido.xml

# BeagleBone board
$ repo init -u https://github.com/geomatsi/yocto-manifests.git -b master -m beagle-legacy-fido.xml  

$ repo sync -c
```

### Prepare yocto build environment

```bash
$ cd poky
$ source oe-init-build-env /home/builder/project/build
```

### Edit build settings

#### bblayers.conf
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

#### local.conf

Replace default machine definition in local.conf.

For pcDuino-Lite-WiFi board:
```asciidoc
MACHINE = "pcduino-lite-wifi"
```

For BeagleBone board:
```asciidoc
MACHINE = "beaglebone"
```

### Building the image

```bash
$ bitbake core-image-minimal
$ bitbake core-image-base
```
