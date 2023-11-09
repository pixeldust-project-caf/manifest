![PixelDust Project](https://github.com/pixeldust-project-caf/manifest/raw/universe/PixelDust_Header.png)
# PixelDust Project CAF - Universe #

## Setting up your machine ##

You must be running a 64-bit Linux distribution and must have installed some packages to build
PixelDust. Google recommends using [Ubuntu](http://www.ubuntu.com/download/desktop) for
this and provides instructions for setting up the system (with Ubuntu-specific commands) on
[the Android Open Source Project website](https://source.android.com/source/initializing.html#setting-up-a-linux-build-environment).

Once you have set up your machine according to the instructions by Google, return here and carry
on with the rest of the instructions.

## Grabbing the source ##

[Repo](http://source.android.com/source/developing.html) is a tool provided by Google that
simplifies using [Git](http://git-scm.com/book) in the context of the Android source.

### Installing Repo ###

```bash
# Make a directory where Repo will be stored and add it to the path
$ mkdir ~/.bin
$ PATH=~/.bin:$PATH

# Download Repo itself
$ curl https://storage.googleapis.com/git-repo-downloads/repo > ~/.bin/repo

# Make Repo executable
$ chmod a+x ~/.bin/repo
```

### Initializing Repo ###

```bash
# Create a directory for the source files
# You can name this directory however you want, just remember to replace
# WORKSPACE with your directory for the rest of this guide.
# This can be located anywhere (as long as the fs is case-sensitive)
$ mkdir WORKSPACE
$ cd WORKSPACE

# Install Repo in the created directory
# Use a real name/email combination, if you intend to submit patches
$ repo init -u https://github.com/pixeldust-project-caf/manifest -b universe
```

### Downloading the source tree ###

This is what you will run each time you want to pull in upstream changes. Keep in mind that on your
first run, it is expected to take a while as it will download all the required Android source files
and their change histories.

```bash
# Let Repo take care of all the hard work
#
# The -j# option specifies the number of concurrent download threads to run.
# 4 threads is a good number for most internet connections.
# You may need to adjust this value if you have a particularly slow connection.
$ repo sync -j$(nproc --all) --force-sync --no-tags --no-clone-bundle
```

#### Syncing specific projects ####

In case you are not interested in syncing all the projects, you can specify what projects you do
want to sync. This can help if, for example, you want to make a quick change and quickly push it
back for review. You should note that this can sometimes cause issues when building if there is
a large change that spans across multiple projects.

```bash
# Specify one or more projects by either name or path

# For example, enter pixeldust-project-caf/android_frameworks_base or
# frameworks/base to sync the frameworks/base repository

$ repo sync PROJECT -j$(nproc --all) --force-sync --no-tags --no-clone-bundle
```

## Building ##

The bundled builder tool `./rom-build.sh` handles all the building steps for the specified device
automatically. As the device value, you just feed it with the device codename (for example,
'hammerhead' for the Nexus 5).

```bash
# Go to the root of the source tree...
$ cd WORKSPACE
# ...and run the builder tool.
$ ./rom-build.sh DEVICE
```

## Submitting Patches ##

We love open source and patches are always welcome!

You can see the status of all patches at [Gerrit Code Review](https://gerrit.pixeldust-project.com/).

```bash
# Start by going to the root of the source tree
$ cd WORKSPACE

# Create a new branch on the specific project you are going to work on
# For example, `repo start fix-clock pixeldust-project-caf/android_frameworks_base`
$ repo start BRANCH pixeldust-project-caf/PROJECT
# You can also use the project path in place of the project name.
# The PROJECT_DIR is the portion after the android_ prefix on
# the PixelDust Github.  For example, android_frameworks_base translates
# into the directory frameworks/base.
# This applies to all repo commands that reference projects.
$ repo start BRANCH PROJECT_DIR
```

### Using plain git to upload ###

```bash
# Go inside the project you are working on
$ cd PROJECT_DIR

# Make your changes
...

# Commit all your changes
$ git add -A
$ git commit -a -s

# Upload your changes
$ git push ssh://USERNAME@gerrit.pixeldust-project.com:29418/pixeldust-project-caf/PROJECT HEAD:refs/for/universe
```

### Extra commands for Gerrit ###

```bash
# If you desire to upload a change as private use the below command
$ git push ssh://USERNAME@gerrit.pixeldust-project.com:29418/pixeldust-project-caf/PROJECT HEAD:refs/for/universe%private

# If you desire to upload a change as W.I.P(Work in Progress) use the below command
$ git push ssh://USERNAME@gerrit.pixeldust-project.com:29418/pixeldust-project-caf/PROJECT HEAD:refs/for/universe%wip

# After that, if you want to make the commit public you can use the UI tools on PixelDust Gerrit website, or use the below command
$ git push ssh://USERNAME@gerrit.pixeldust-project.com:29418/pixeldust-project-caf/PROJECT HEAD:refs/for/universe%remove-private

# If you want to unset the W.I.P status on your commit, you can use UI tools on PixelDust Gerrit website, or use the below command
$ git push ssh://USERNAME@gerrit.pixeldust-project.com:29418/pixeldust-project-caf/PROJECT HEAD:refs/for/universe%ready
```

### Making additional changes ###

If you are going to make more changes, you just have to repeat the steps (except for `repo start`
which you should not repeat) while using `git commit --amend` instead of `git commit -a -s` so that
you avoid having multiple commits for this single change. Gerrit will then recognize these changes
as a new patch set and figure out everything for you when you upload.

### Squashing multiple commits ###

Your patches should be single commits. If you have multiple commits laying around, squash them by
running `git rebase -i HEAD~<commit-count>` before uploading.

### Writing good commit messages ###

You will be asked a commit message when you run `git commit`. Writing a good commit message is
often hard, but it is also essential as these messages will stay around with your changes and
will be seen by others when looking back at the project history.

A few general pointers to keep in mind when writing the commit message are that you should use
imperative as it matches the style used by the `git merge` and `git revert` commands (that means
"Fix bug" is preferred over "Fixes bug", "Fixed bug" and others) and that you should write the
first line of the commit message as a summary of the commit. It should always be capitalized and
followed by an empty line. You might optionally include the project name at the start and try to
keep it to 50 characters when possible as it is used in various logs, including "one line" logs.

### Code ###

Our codebase is licensed under Apache License, Version 2.0 unless otherwise specified. Apache
License 2.0 allows a variety of actions on the content as long as licensing and copyright
notices are retained and included with the code and your changes to the codebase are stated.

You can read the full license text at http://www.apache.org/licenses/LICENSE-2.0

### Images & other assets ###

Unless otherwise specified, all our assets, including but not limited to images, are licensed
under Creative Commons Attribution-NonCommercial 4.0 International, or CC BY-NC 4.0 for short.
This means that you are allowed to modify the aforementioned assets in any way you want and
you are free to share the originals and/or the modified work. However, you are not allowed
to use the assets for commercial purposes and you must provide attribution at all times which
means you have to include a short note about the license used (CC BY-NC 4.0), the original
author/authors (PixelDust Project) and inform about any changes that have been
made.

You can reach the full legal text at http://creativecommons.org/licenses/by-nc/4.0/
