---
title: Installation
---

It is possible to install the CLI tool `ioc` and the library `libioc` independently. This instructions will install the CLI and its matching library version.

Please clone the repository from: https://github.com/bsdci/ioc.git

```shell-session
~src # git clone https://github.com/bsdci/ioc.git
~src # cd ioc
```

A global installation can be performed by using the Makefile. This step will automatically checkout the right commit of the library submodule.

```shell-session
~libioc # make install
```
### Development

It is not required to install libioc or ioc globally for development purposes. It is enough to check out the git submodule and install the dependencies to use (lib)ioc from the local directory.

```sh
git clone --recurse-submodules https://github.com/bsdci/ioc.git
cd ioc/
make -C .libioc install-deps-dev
make deps

python3.6 . --version
```

To run the style checkers and test suite, the Python dependencies from requirements-dev.txt can be installed additionally:

```sh
python3.6 -m pip install -Ur requirements-dev.txt
```
