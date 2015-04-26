
## Dreamcat's build tool - "drb"

Dreamcat's builder. A simple shell-based build tool.

This tool is written in shell, to be run ubuntu. It has been created to make easier cross-compile and build OS disk images for embedded arm hardware platforms such as rpi2, odroid-c1, etc. However `drb` is a reasonably generic build tool and may be used instead in some situations instead of `make`.

### Build targets

This program operates entirely on a plugins concept, whereby a build target is a plugin that implements a certain set of standardized plugin API methods. Each build target is a folder of files that contain all instructions and other meta-information needed to build the target.

Build targets can be downloaded from online sources with one of the provided generic 'installer' build targets.

```sh
  # a build target's file structure
  TARGET_NAME/
    config.default       # template user config file
    supported.platforms  # list of target 'platforms' this target can build, e.g. 'rpi2'
    target.sh            # implementation of target-specific build functions (API methods)
```

### Installation

Git clone from master branch

```sh
git clone https://github.com/dreamcat4/drb
sudo mkdir -p /usr/local/bin
cd drb && sudo ln -s $PWD/usr/bin/drb /usr/local/bin/
```

From my launchpad PPA: ! `.deb` pkg does not exist yet

```sh
# ! sudo add-apt-repository -y dreamcat4:ppa
# ! sudo apt-get update  && sudo apt-get install drb
```

### CmdLine Args


### Todo

!targets
  * implement meta-build target (the "hidden" flag)

!info
  * information about a target

!configure
  * implement 'configure' command to:
      cp target.config file --> ~/.drb/config
      launch $EDITOR on copy of config file

!check
  * drb check target
      write a cmd to check targets for compliancy

* write documentation of
    target developer's API (functions)

* implement default-targets for c1



## Installation

```sh
# Download drb
git clone git@github.com:dreamcat4/odroid-c1-builder.git ~/drb

# Add the 'drb' executable to your $PATH
PATH="${PATH}:${HOME}/drb/usr/bin/drb"

# Download the latest version of default targets
drb sync default-targets

# Or you can download alternative build targets from non-default
# repo online sources, by editing: '~/.drb/config/user.config'

# List available build targets
drb targets
```


## Usage

### Check apt dependancies

```sh
target="android"

# Print the list of apt-packages required to build the target OS
drb depends $target
```

Then install the missing apt dependancies with `sudo apt-get install <missing-pkgs>`.

### Perform all build actions in one step

```sh
target="android"
drb image $target
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
drb image $target
```

### Cleaning

```sh
target="android"

# Delete the build files, dd image files, but keep the source code
drb clean $target

# Delete the build files, dd image files, AND source code (everything)
drb distclean $target
```


