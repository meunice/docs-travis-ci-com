---
title: The Xenial Build Environment
layout: en
---

> Xenial LTS Standard has reached End of Life (EOL) as per Canonical. Consider updating to a newer image. Travis CI has ceased all work on updates to Xenial images as of the end of the 2024 calendar year, and it is now considered deprecated.

## What This Guide Covers

This guide provides an overview of the packages, tools and settings available in the Xenial environment.

## Using Xenial

To route your builds to Ubuntu Xenial 16.04, add the following to your `.travis.yml`:

```yaml
dist: xenial
```
{: data-file=".travis.yml"}

Please note that Xenial is available on our hosted fully virtualized
infrastructure. If you are running an Enterprise installation, please reach out
to [enterprise@travis-ci.com](mailto:enterprise@travis-ci.com?subject=Try%20out%20Xenial) to see how you can use the Xenial Docker images.

## Differences from the Trusty images

Xenial includes the following changes and improvements:

### Third party apt-repositories removed

While third party apt-repositories are used during the Xenial image provisioning, they are all removed from the Xenial build image. This has two benefits; a) reduced risk of unrelated interference and b) faster apt-get updates.

To specify a third party apt-repository, you can [add the source with the apt addon](/user/installing-dependencies/#adding-apt-sources) and specify the packages. For example:

```yaml
dist: xenial
addons:
  apt:
    sources:
      - ppa:chris-lea/redis-server
    packages:
    - redis-tools
    - redis-server
```
{: data-file=".travis.yml"}

If you depend on these repositories in your build, you can use the following `source` line to get them back:

| package              | source                       |
|:---------------------|:-----------------------------|
| couchdb              | `ppa:couchdb/stable`         |
| docker               | `docker-xenial`              |
| google-chrome-stable | `google-chrome`              |
| git-lfs              | `github-git-lfs-xenial`      |
| git-ppa              | `ppa:git-core/ppa`           |
| haskell              | `ppa:hvr/ghc`                |
| mongodb              | `mongodb-4.0-xenial`         |
| pollinate            | `ppa:pollinate/ppa`          |
| redis                | `ppa:chris-lea/redis-server` |
{: style="width: 80%" }

### Services disabled by default

On our Xenial infrastructure, to speed up boot time and improve performance we've disabled all services, including the ones that are started by default on Trusty.
Add any services that you want to start by default to your `.travis.yml`:


```yaml
services:
  - mysql
  - redis
```
{: data-file=".travis.yml"}

## Environment common to all Xenial images

The following versions of Docker, version control software and compilers are present on all builds, along with more language specific software described in more detail in each language section.

All preinstalled software not provided by distro is installed from an official release --
either a prebuilt binary if available, or a source release built with default options.
For preinstalled language interpreters, a standard version manager like `rvm` is used if available for the language.

### Version control

| package | version  |
|:--------|:---------|
| git     | `2.37.3` |
| git-lfs | `3.2.0`  |
| hg      | `5.9.3`  |
| svn     | `1.9.3`  |
{: style="width: 30%" }

### Compilers and Build toolchain

* clang and llvm 7
* cmake 3.12.4
* gcc 5.4.0
* ccache 3.2.4-1
* shellcheck 0.7.2
* shfmt 3.2.1

To use the IBM Advance Toolchain v12 compilers under `ppc64le` architecture in Xenial LXD image, use the following paths in your `.travis.yml`:

- GCC compiler
  - Path: `/opt/at12.0/bin/gcc`
  - Command: `/opt/at12.0/bin/gcc hello_world.c -o hello_world`

- g++ compiler
  - Path: `/opt/at12.0/bin/g++`
  - Command: `/opt/at12.0/bin/g++ hello_world.cpp -o hello_world`

- Go compiler
  - Path: `/opt/at12.0/bin/gccgo`
  - Command: `/opt/at12.0/bin/gccgo hello_world.go -o hello_world`

- Python
  - First, compile Python 3.8.0 using the `python_interpreter.sh script`.
  - Python Interpreter Path: `/opt/python380-at12/python3.8`
  - Build Python Command: `sudo sh python_interpreter.sh`

To use the IBM Advance Toolchain v12 compilers under `amd64` architecture in Xenial LXD image, use the following paths in your `.travis.yml`:

- GCC compiler
  - Path: `/opt/at12.0/bin/powerpc64le-linux-gnu-gcc`
  - Command: `/opt/at12.0/bin/powerpc64le-linux-gnu-gcc hello_world.c -o hello_world`

- g++ compiler
  - Path: `/opt/at12.0/bin/powerpc64le-linux-gnu-g++`
  - Command: `/opt/at12.0/bin/powerpc64le-linux-gnu-g++ hello_world.cpp -o hello_world`

- Go compiler
  - Path: `/opt/at12.0/bin/powerpc64le-linux-gnu-gccgo`
  - Command: `/opt/at12.0/bin/powerpc64le-linux-gnu-gccgo hello_world.go -o hello_world`

- Python
  - First, compile Python 3.8.0 using the `python_interpreter.sh script`
  - Python Interpreter Path: `/opt/python380-amd64/python3.8`
  - Build Python Command: `sudo sh python_interpreter.sh`

### Docker

* Docker 20.10.7 is installed
* docker-compose 1.29.2.

## Ruby support

* Pre-installed Rubies: `2.7.6`, `3.0.4` and `3.1.2`.
* The default ruby is `2.7.6p219`.
* Other ruby versions can be installed during build time.

## Python support

* Supported Python versions: `3.5` or higher.
* Pre-installed Python versions: `3.7.13`, and `3.8.13`.
* Python `3.7.13` will be used when no language version is explicitly set.

If you're getting errors about PyPy `pypy is not installed; attempting download`, use one of the more recent python versions.

## JavaScript and Node.js support

* For builds specifying `language: node_js`, `nvm` is automatically updated to the latest version at build time. For other builds, the stable version at image build time has been selected, which is 0.39.1.
* NodeJS version `16.16.0` is preinstalled.

## Go support

* Pre-installed Go: `1.18.4`

* Other Go versions can be installed during build time by specifying the language versions with the `go:`-key.

## JVM (Clojure, Groovy, Java, Scala) support

* Pre-installed JVMs: `openjdk8` and `openjdk11` on x86, default
is `openjdk11`; `openjdk7` and `openjdk8` on ppc64le.

* Other JDKs, including Oracle's, can be acquired if available by specifying `jdk`.

* The following table summarizes the Pre-installed JVM tooling versions:

| package | version |
|:--------|:--------|
| gradle  | 7.4.2   |
| maven   | 3.8.6   |
| groovy  | 4.0.3   |
{: style="width: 30%" }

## Perl support

* Default version on Xenial is `5.34.1`
* Supported versions `5.22`, `5.24`, `5.26`, `5.28`, `5.29`, `5.30`, `5.31`, `5.32`, `5.33` and `5.34` can be installed by using the `perl:`-key.
* `TAP::Harness` v3.35 and `cpanm` (App::cpanminus) version 1.7044 are also pre-installed.

## PHP support

* For dynamic runtime selection, `phpenv` is available.
* PHP version `7.4.30` is preinstalled.

## Databases and services

The following services and databases are preinstalled but but do not run by default.
To use one in your build, add it to the services key in your `.travis.yml` :

| service    | version        |
|:-----------|:---------------|
| mongodb    | 4.0.28         |
| mysql      | 5.7.33         |
| redis      | 6.0.6          |
| postgresql | 9.4 9.5 9.6 10 |
{: style="width: 30%" }

* IMPORTANT: Since Xenial LTS is officaly EOL, postgre apt packages are no more available from apt.postgresql.org repository: https://wiki.postgresql.org/wiki/Apt
* Currently Xenial is using apt-archive.postgresql.org, unfortunately it lacks ARM and S390x arch packages.

## Other Ubuntu Linux Build Environments

You can have a look at the [Ubuntu Linux overview page](/user/reference/linux/) for the different Ubuntu Linux build environments you can use.
