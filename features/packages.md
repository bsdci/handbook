---
title: Packages
---
Packages can be installed to jails even without access to the Internet.
When using the Pkg feature of libiocage, packages are mirrored locally and mounted into the jail for package installation.

```sh
{{site.ioc_cli_tool}} pkg my-jail vim-console git
```

Because package sources and local mirror assets match to a Release, it has to be passed to the [Pkg.fetch](https://ioc.github.io/libiocage/ioc.Pkg.html#ioc.Pkg.Pkg.fetch) method of libioc.
The release is automatically selected from the jail when installing packages.

```python
import ioc
pkg = ioc.Pkg()

jail = ioc.Jail("my-jail")
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