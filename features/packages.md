---
title: Packages
---
Packages can be installed to jails even without access to the Internet.
When using the Pkg feature of libiocage, packages are mirrored locally and mounted into the jail for package installation.

```sh
{{site.iocage_cli_tool}} pkg my-jail vim-console git
```

Because package sources and local mirror assets match to a Release, it has to be passed to the [Pkg.fetch](https://iocage.github.io/libiocage/iocage.Pkg.html#iocage.Pkg.Pkg.fetch) method of libiocage.
The release is automatically selected from the jail when installing packages.

```python
import iocage
pkg = iocage.Pkg()

jail = iocage.Jail("my-jail")
packages = ["vim-console", "git"]

pkg.fetch(
    packages=packages,
    release=jail.release
)

pkg.install(
    packages=packages,
    jail=jail
)
```