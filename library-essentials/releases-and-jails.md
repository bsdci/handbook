Jail and Release both inherit from the Resource class.
Analog to the Resource-classes two further classes Jails and Releases (plural) inherit from the same class, the ListableResource.

Both plural classes are used to filter in lists of Jails or Releases, for example by using jail name globbing from the commandline to select all iocage Jails with a name beginning with `myproject-`:

```sh
{{site.iocage_cli_tool}} list myproject-+
```

The same can be achieved with the Python module

```python
jails = iocage.Jails("myproject-+")
for jail in jails:
	print(jail.name)
```

### Filter Terms

FilterTerms are a list of individual Filter.Term instances that can either match the Resource name.
The Terms are constructed from an input string that may either be a Jail name (with globbing) or match against a variable.

#### Example: Filtering for running jails

```sh
{{site.iocage_cli_tool}} list running=true
```

```python
filters = ("running=yes",)
jails = iocage.Jails(filters=filters)
running_jails = list(jails)
```

#### Wildcards / Globbing

As shown above `+` and `*` are globbing characters that match a Resources name.

| Character | Effect                         |
|-----------|--------------------------------|
| `*`       | Matches none or any characrer  |
| `+`       | Matches at least one character |

Note: For efficiency reasons filtering for Resource names matches against the Resource dataset name early on.
Names are matched before any other filters are applied, so that it is not necessary to query the JailState or reading the JailConfig when the name does not match at all.

#### Config and State Filters

When a resource has a BaseConfig or JailState objects attached, the filters containing an equal sign `=` are matched against attributes of the dictionary structures.
This enables iocage to efficiently select jails matching the input criteria.

#### Filter Operators

iocage resource filters can be combined using `AND` or `OR` operators.
When filters contain Terms for the same JailConfig, JailState or the Resource name, both are required to match.

The following filter will match all jails with a name beginning with `foo` that also contain `bar` in their name.

```sh
{{site.iocage_cli_tool}} list foo+ +bar+
```

Unlike the filter above it is possible to select jails that either begin with `foo` or contain `bar` in their name by separating the terms with a comma:

```sh
{{site.iocage_cli_tool}} list "foo+,*bar*"
```

Note: Depending on the shell it is required to quote or escape filter terms containing the `*` or `+` glob characters.
Similarly the `,` character used in most shells for ordering commands, which need quoting or escaping.
