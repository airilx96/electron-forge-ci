# Overview
Distributing an Electron Application via tools, configuration and continuous integration services.

## What this covers
This covers what it takes to go from running `npm start` to being able to download and install the application.
This documentation is for Electron based application developers to better understand the process involved.
- Electron: https://electron.atom.io/

While these can be complicated steps, a single tool partnered with Continuous Integration services can make this much easier to deal with than using separate tools and scripts.

## Steps
- Package (via electron-forge)
- Build/Make (via electron-forge)
- Publish/Release (via continuous integration services)
- Tag/Version (via script)

# Tools
- electron-forge

## Electron-Forge
https://electronforge.io/

Electron-Forge is used bootstrap templated electron apps including react ready ones to help you get started creating an app, including many best practices such as babel, web-pack, es-linters like air-bnb. What it really offers us is abstracting the complicated Packaging and Building processes so that we don't have to.

### Electron-Forge Configuration
I've prepared a sample electron app using electron-forge that you can browse to see how simple the configuration can be with no scripts. The configuration is basic and can be expanded to cover details such as mac/windows specific icons.
- Documentation: https://github.com/electron-userland/electron-forge/blob/master/README.md
- Sample config: https://github.com/klappy/electron-forge/blob/master/.forge.config.json

The first section, `make_targets`, tells the current OS the desired build target. Windows uses Squirrel that generates nice .exe installers. Darwin/OSX/Mac uses a DMG image for installation and compressed images. For Linux we will use DEB packages since that's Ubuntu uses and its the most common Desktop flavor of Linux.

#### OS and Architecture
Electron-forge only builds for the OS and Architecture it is run on. While that seems limited at first, we choose to run on continuous integration servers that provide the desired OS builds.
- Windows: https://github.com/electron/windows-installer#usage
- Mac/OSX/Darwin: https://github.com/mongodb-js/electron-installer-dmg#api

### Electron-Forge Packaging
It's as easy as `npm run package` which via package.json scripts is an alias for `electron-forge package`. This runs webpack and takes care of es6/babel type stuff behind the scenes so we don't have to, including copying them to a path that is non-destructive and won't be committed to our codebase since our .gitignore file ignores the `out` folder.

### Electron-Forge Building/Making
It's as easy as `npm run make` which via package.json scripts is an alias for `electron-forge make`. This runs electron-build behind the scenes with many default configurations and scripts for each OS that abstracts the complications. We can configure this via special sections for each OS.

### Electron-Forge Publishing/Releasing
We don't use this as we want to let this be done via our continuous integration services.

# Continuous Integration
Continuous Integration (CI) allows us to use git hooks to run things like automated tests on our code on their servers and run our package/build/publishing automagically via our configurations.

## Continuous Integration Services
- Travis-CI for Mac/Linux (travis-ci.org)
- Appveyor for Windows (appveyor.com)

### Travis-CI
Travis-CI is the standard in Mac/Linux CI services.
Here is a sample .travis.yml configuration file that can run tests and package/build/publish the sample app.
- Commented sample config: https://github.com/klappy/electron-forge/blob/master/.travis.yml
- Releases Documentation: https://docs.travis-ci.com/user/deployment/releases/

### Appveyor
Appveyor is the standard in Windows CI services.
Here is a sample appveyor.yml configuration file that can run tests and package/build/publish the sample app.
- Commented sample config: https://github.com/klappy/electron-forge/blob/master/appveyor.yml
- Releases Documentation: https://www.appveyor.com/docs/deployment/github/
- Choosing files to release: https://www.appveyor.com/docs/packaging-artifacts/

## Tag/Version
Before you make a release, the first step is making sure your tests pass and you have a working version of your app. After you are sure you have your codebase ready for primetime or at least QA testing, you can now create a tag to trigger all of the above automagically. 

The sample script is aliased in the package.json so it can be run as `npm run update-version x.x.x-beta.x`. Basically what it does is updates the version of your app via npm version command and then after committing to your branch, creates a tag that is then pushed up to github to trigger continuous integration services to package/build/deploy for you. Version numbers aren't arbitrary and NPM/Github/Travis/Appveyor all use the same conventions for versioning including namespaces for `major.minor.patch-[alpha/beta].build`.
- Sample version update script: https://github.com/klappy/electron-forge/blob/master/scripts/updateVersion.js

Other areas that are affected by this are automated testing and ...
