---
title: Recipes
description: A collection of common/useful code examples for Kotlin
---


# Recipes

We've written some recipes that demonstrate how to solve common problems with Paparazzzi. Read through them to learn about how everything works together. Cut-and-paste these examples freely; that's what they're for.

### Target a specific SDK level

You can make Paparazzi to target a specific SDK level on your test by changing your `@get:Rule` to the following:

```kotlin
@get:Rule val paparazzi = Paparazzi(
  theme = "android:Theme.Material.Light.NoActionBar",
  environment = detectEnvironment().copy(
    platformDir = "${androidHome()}/platforms/android-32",
    compileSdkVersion = 32
  )
)
```

### Usage of Git LFS

It is recommended you use [Git LFS][lfs] to store your snapshots.  Here's a quick setup:

```bash
$ brew install git-lfs
$ git config core.hooksPath  # optional, confirm where your git hooks will be installed
$ git lfs install --local
$ git lfs track "**/snapshots/**/*.png"
$ git add .gitattributes
```

On CI, you might set up something like:

`$HOOKS_DIR/pre-receive`
```bash
# compares files that match .gitattributes filter to those actually tracked by git-lfs
diff <(git ls-files ':(attr:filter=lfs)' | sort) <(git lfs ls-files -n | sort) >/dev/null

ret=$?
if [[ $ret -ne 0 ]]; then
  echo >&2 "This remote has detected files committed without using Git LFS. Run 'brew install git-lfs && git lfs install' to install it and re-commit your files.";
  exit 1;
fi
```

`your_build_script.sh`
```bash
if [[ is running snapshot tests ]]; then
  # fail fast if files not checked in using git lfs
  "$HOOKS_DIR"/pre-receive
  git lfs install --local
  git lfs pull
fi
```


