## Dreamcat's build tool

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
 

  - [Introduction](#introduction)
  - [Requirements](#requirements)
  - [Build targets](#build-targets)
  - [Installation](#installation)
  - [CmdLine Args](#cmdline-args)
  - [Todo](#todo)
- [Downloading build targets](#downloading-build-targets)
- [Usage](#usage)
  - [Global Config](#global-config)
  - [Writing a Build Target](#writing-a-build-target)
  - [Perform all build actions in one step](#perform-all-build-actions-in-one-step)
  - [Hacking a custom build, hacking as-you-go](#hacking-a-custom-build-hacking-as-you-go)
  - [Cleaning](#cleaning)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

### Introduction

Dreamcat's build tool. A simple shell-based build tool.

This tool has been created as a wrapper to run drb build targets. To download, compile and build OS disk images for embedded arm hardware platforms. Such as rpi2, odroid-c1, etc.

***Rationale:***

> "Because we do not want to go through some complex build proceedure, just to apply a kernel patch."

What makes the `drb` program *useful* is that it's build targets are constructed of generic `sh` shell script. Split into a few coarse build stages. Any arbitrary shell commands can be used implement the fetch, build etc. With just enough minimum flexibility so that ordinary users can modify their build with simple tweaks / patches etc. And no fuss.

By sticking *entirely to generic shell script, with a very lightweight API structure around that*. This allows us to *very rapidly convert and transfer* those pre-existing and already-established community build proceedures, into becoming Drb build targets. *Whilst with a lowest possible barrier of entry to community members* to re-write their existing shell-based build scripts into the simple Drb API format.

***History:***

Drb was brought into existence specifically for the Odroid-C1 user community. Actually, it was originally named "odroid-c1-builder". However it may eventually prove to be of some questionable value in other similar sibiling communities. Such as for odroid-*, rpi2, andso on. With this in mind, `ocb` has been renamed to `drb`. And is now an entirely generic build tool. Which is seperately managed any odroid code. All odroid-c1 specific (or other such platform) are implemented within the build targets. Which is a plugin architecture.

***Note:***

On the surface, this tool looks similar to `buildroot`. However Drb was never designed to compete with `buildroot` or any of it's other more heavyweight equivalents such as `make`, `yocto`, and `gradle`. That is not Drb's role in the user community context. But rather, Dreamcat's Builder is just meant to be a unified top layer. A wrapper. All is uniformly scripted, and encapsulated behind `drb`'s much simpler, more user-friendly interface.

If you don't already need `drb`, or don't understand what it is for... then don't use it! Most of the value of the `drb` tool is in it's community oriented build targets. Which are very community-driven and community specific. For a good general purpose build tool, I recommend GNU make.

### Requirements

* Ubuntu 14.04 or higher
* To use `drb` on other linux host distros, you should just run it inside of a docker image.
* Build dependancies from 3rd party PPAs are not supported ATM.

The only requirement of drb is that it should be run on an ubuntu host system. This simplifies the testing of build dependancies, when all targets are written and tested on ubuntu hosts. This choice makes for simplest management of build dependancies, relying upon `apt-get` tool.

* If you do wish to port the drb tool to some non-linux platform, e.g. FreeBSD. Then you will need to modify the code around `apt_depends`.

### Build targets

This program operates entirely on a plugins concept, whereby a build target is a plugin that implements a certain set of standardized plugin API methods. Each build target is a folder of files that contain all instructions and other meta-information needed to build the target.

Build targets can be downloaded from online sources with one of the provided generic 'installer' build targets.

```sh
  # a build target's file structure
  TARGET_NAME/
    config.default       # template user config file
    supported.platforms  # list of target 'platforms' this target can build, e.g. 'rpi2'
    target.depends       # load another target first, so it can be called from this one
    target.sh            # implementation of target-specific build functions (API methods)
```

### Installation

Git clone from master branch

```sh
# Download drb
git clone https://github.com/dreamcat4/drb && cd drb

# Add the 'drb' executable to your $PATH
sudo mkdir -p /usr/local/bin
sudo ln -s $PWD/usr/bin/drb /usr/local/bin/
```

From my launchpad PPA: ! `.deb` pkg does not exist yet

```sh
# ! sudo add-apt-repository -y dreamcat4:ppa
# ! sudo apt-get update  && sudo apt-get install drb
```

### CmdLine Args

```sh
 drb:
      Dreamcat\'s builder. A simple shell-based build tool.
      'man drb' for more help / information.

 Usage:
      $ drb <cmds> [platforms] [--] [targets]

 Commands:

      targets    - List available build targets
      configure  - Make user copy of target config & open in editor.
      !info       - Show metadata info about specific targets
      select     - Set a build alias to point to a different target
      depends    - Check depenancies. Install missing packages.
      fetch      - Download source code/files needed to build the target
      sync       - Update fetched source code to the latest version
      build      - Compile/make all intermediate build-time files
      assemble   - Create the final build product output files
      clean      - Remove build files
      distclean  - Remove all build files + src files

 Platforms:
      Hardware platform(s) you wish to build targets for.
      Targets you build must include support for those platform(s).
      e.g. 'c1', 'u3', 'xu3' etc. Not all targets support all platforms.

 Targets:

      -x,--debug     - Print full command trace with set -x
      -v,--version   - Print the current version of drb and exit.
      -h,--help      - Display this message and exit.

 Version:
      0.01 pre-alpha
```

### Todo

!info
  * information about a target

* write documentation of
    target developer's API (functions)

* implement default-targets for c1


## Usage

### Global Config

```sh
EDITOR=cat drb configure drb
```

### Downloading build targets

! not implemented yet

```sh
# List the available target sets that come pre-installed with drb
drb targets

# Download the latest version of the available official targets
drb sync c1-targets

# Or to configure alternative build targets from your own repos:
cp -Rf /usr/share/drb/targets/default/c1-targets ~/.drb/target/my-targets
nano ~/.drb/target/my-targets/config.default

# Then
drb sync my-targets

# List again the available build targets
drb targets
```

### Writing a Build Target

This will be documented in the online examlples.

```sh
drb fetch examples
drb info example
```

### Hacking a custom build, hacking as-you-go

```sh
target="android"

# Download source files
drb fetch $target

# Update source files (e.g. git pull, repo sync, etc.)
drb sync $target

# Hack on source code, add your own patches

# Build (e.g. make all)
drb build $target

# Modify build products, add your own config, pre-seeded data files, etc

# Create dd disk image
drb assemble $target
```

### Performing all build actions in one single step

```sh
# All prior actions will be completed first: fetch, build, sync
drb assemble $target
```

### Cleaning

```sh
target="android"

# Delete the build files, output files, but keep the source code
drb clean $target

# Delete the build files, output files, AND source code (everything)
drb distclean $target
```


