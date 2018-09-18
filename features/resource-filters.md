Filter selection of bunches of jails makes it easy to target a bunch of jails.
A filter consists of one or many filter terms that will be matched against any jails.
Jails passing all filter terms will then be suspect of further operations.
The easiest way get an impression the feature is the `{{site.iocage_cli_tool}} list` command where the output gives visual feedback of matching filters.

These jails will be used for demonstration purposes:

```sh
{{site.iocage_cli_tool}} create my-project-a vnet=on tag=customer-a
{{site.iocage_cli_tool}} create my-project-b vnet=off tag=customer-a
{{site.iocage_cli_tool}} create other-jail-a vnet=off tag=customer-b
{{site.iocage_cli_tool}} create other-jail-b vnet=on tag=customer-b
```

The simplest form of filtering is just a jail name:

```sh
{{site.iocage_cli_tool}} list my-project-a
```

#### Globbing

The glob characters `+` and `*` match any string - also matches to an empty string.

Note: The default shell requires escaping the `*` character, which is why this handbook always puts them in quotes.

Only Jails with a name that starts with `my-project` are listed:
```
{{site.iocage_cli_tool}} list "my-project+"
```

#### Filtering by Jail-Config properties

Not only Jail names can be matched, but also all their properties, which enables users to 

```sh
{{site.iocage_cli_tool}} list vnet=on
```

#### AND operator

All provided filters have to match a jail to pass the filter.
Filtering for all Jails that have VNET enabled and a name beginning with `other`.

```sh
{{site.iocage_cli_tool}} list "other*" vnet=on
```

#### OR operator

Filter terms can be comma separated to define alternatives.
Either value has to match to pass the filter.

```sh
{{site.iocage_cli_tool}} list "other*" tag=customer-a,
```

The above example prints all jails that are tagged with `customer-a` or do not have a tag at all.

#### Filters in the Python Module

The iocage Python module accepts Filters as selector of Resources like Jails and Releases. They can be passed to the `JailsGenerator` or `ReleasesGenerator` (plural) classes as Python set structure.

```python
>>> import iocage
>>> jails = iocage.Jails(("tag=customer-a",))
>>> [jail.name for jail in jails]
[
  "my-project-a",
  "my-project-b"
]
```