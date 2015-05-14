## Dreamcat's build tool

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
 

- [Introduction](#introduction)
  - [Rationale](#rationale)
  - [History](#history)
  - [Other build tools](#other-build-tools)
- [Requirements](#requirements)
- [Build targets](#build-targets)
- [Installation](#installation)
- [CmdLine Args](#cmdline-args)
- [Usage](#usage)
  - [Configuration](#configuration)
  - [Downloading build targets](#downloading-build-targets)
  - [Writing a Build Target](#writing-a-build-target)
  - [Hacking a custom build, hacking as-you-go](#hacking-a-custom-build-hacking-as-you-go)
  - [Performing all build actions in one single step](#performing-all-build-actions-in-one-single-step)
  - [Cleaning](#cleaning)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

### Introduction

Dreamcat's build tool. A simple shell-based build tool.

This tool has been created as a wrapper to run drb build targets. To download, compile and build OS disk images for embedded arm hardware platforms. Such as rpi2, odroid-c1, etc.

#### Rationale

> "Because we do not want to go through some complex build proceedure, just to apply a kernel patch."

What makes the `drb` program *useful* is that it's build targets are constructed of generic `sh` shell script. Split into a few coarse build stages. Any arbitrary shell commands can be used implement the fetch, build etc. With just enough minimum flexibility so that ordinary users can modify their build with simple tweaks / patches etc. And no fuss.

By sticking *entirely to generic shell script, with a very lightweight API structure around that*. This allows us to *very rapidly convert and transfer* those pre-existing and already-established community build proceedures, into becoming Drb build targets. *Whilst with a lowest possible barrier of entry to community members* to re-write their existing shell-based build scripts into the simple Drb API format.

#### History

Drb was brought into existence specifically for the Odroid-C1 user community. Actually, it was originally named "odroid-c1-builder". However it may eventually prove to be of some questionable value in other similar sibiling communities. Such as for odroid-*, rpi2, andso on. With this in mind, `ocb` has been renamed to `drb`. And is now an entirely generic build tool. Which is seperately managed any odroid code. All odroid-c1 specific (or other such platform) are implemented within the build targets. Which is a plugin architecture.

#### Other build tools

On the surface, this tool looks similar to `buildroot`. However Drb was never designed to compete with `buildroot` or any of it's other more heavyweight equivalents such as `make`, `yocto`, and `gradle`. That is not Drb's role in the user community context. But rather, Dreamcat's Builder is just meant to be a unified top layer. A wrapper. All is uniformly scripted, and encapsulated behind `drb`'s much simpler, more user-friendly interface.

If you don't already need `drb`, or don't understand what it is for... then don't use it! Most of the value of the `drb` tool is in it's community's build targets. Where the support and benefits provided by this tool are very much linked to those community's situation. Instead of that, if you just need a general purpose build tool, I recommend autotools + GNU make.

### Requirements

* Ubuntu 14.04 or higher.

Else you can compile `drb` build targets on other linux host distros, if you install docker and run drb inside of a docker image.

The only hard requirement of the drb tool itself is bash. However considering that almost all drb build targets are written and tested to be build on an x86 (intel based) ubuntu host system. Then that is what you should be using. As it greatly simplifies the assumptions of build dependancies etc, when all targets are written and tested on ubuntu hosts. This choice makes for simplest management of build dependancies, mostly just relying on the standard `apt-get` tool.

If you do wish to write build targets for the drb tool on some other non-deb based platform, e.g. FreeBSD. Then you cannot specify your pkg dependancies drb's built-in `apt_depends` dependancy checking feature. You need to hard-code pkg dependancy checking and installation within your target's custom [pre_]setup() function(s), etc.

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

! Not implemented yet. From my launchpad PPA: ! `.deb` pkg does not exist yet

```sh
# ! sudo add-apt-repository -y dreamcat4:ppa
# ! sudo apt-get update  && sudo apt-get install drb
```

### CmdLine Args

```sh
 drb:
      Dreamcats build tool. A simple shell-based framework.
      Visit: https://github.com/dreamcat4/drb for help guide.

 Usage:
      $ drb <cmds> [platforms] [--] [targets]

 Commands:
      targets    - List available build targets
      configure  - Make user copy of target config & open in editor
      info       - Show metadata info about specific targets
      select     - Set a build alias to point to a different target
      depends    - Check target depenancies

 Build Stages:
      fetch      - Download all source code needed to build the target
      sync       - Update the target source code to its latest version
      build      - Compile/make all build-time and intermediate files
      assemble   - Create the final build product and output files
      clean      - Remove build files
      distclean  - Remove build files and src files

 Platforms:
      The hardware platform(s) you wish to build targets for.
      Targets you build must be written to support those platform(s).
      e.g. 'c1', 'rpi', 'beaglebone', etc.

 Options:
      -x,--debug     - Print command trace of targets
      -X,--trace     - Print command trace of drb program
      -v,--version   - Print the current version of drb and exit.
      -h,--help      - Display this message and exit.

 Version:
      0.20 beta
```

### Usage

#### Configuration

```sh
# Show global configuration
EDITOR=cat drb configure

# Edit global configuration
drb configure

# Edit target configuration
drb configure $target

```

#### Downloading build targets

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

#### Writing a Build Target

! not implemented yet. This will be documented in the online examlples.

```sh
drb fetch examples
drb info example
```

#### Hacking a custom build, hacking as-you-go

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

#### Performing all build actions in one single step

```sh
# All prior actions will be completed first: fetch, build, sync
drb assemble $target
```

#### Cleaning

```sh
target="android"

# Delete the build files, output files, but keep the source code
drb clean $target

# Delete the build files, output files, AND source code (everything)
drb distclean $target
```


