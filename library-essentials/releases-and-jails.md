Jail and Release both inherit from the Resource class. Analog to the Resource-classes two further classes Jails and Releases (plural) inherit from the same class, the ListableResource.

Both plural classes are used to filter in lists of Jails or Releases, for example by using jail name globbing from the commandline to select all iocage Jails with a name beginning with `myproject-`:

```sh
{{site.iocage_cli_tool}} list myproject-+
```

The same can be achieved with the Python module

```python
jails = iocage.Jails("myproject-+")
```

#### Filter Terms

##### Wildcards / Globbing

As shown above `+` and `*` are globbing characters that match a Resources name.

| Character | Effect                         |
|-----------|--------------------------------|
| `*`       | Matches none or any characrer  |
| `+`       | Matches at least one character |