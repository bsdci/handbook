The iocage library Python is not supposed to print to stdout directly.
Objects that are supposed to log do this by calling the Logger instance that was initialized on object creation.
Each object that has a logger will automatically pass it to further objects they create which makes it sufficient to pass the Logger to the first iocage object that is created.

By default the logger only prints erros and warnings which may relate to an IocageException.
Due to this default the {{site.iocage_cli_tool}}} commands can simply `exit(1)` and leave informing users up to the logger.

### Print Level

An iocage Logger has a `print_level` attribute that defines the minimal loglevel of a message to be printed.
The value defaults to `warn`, but can be changed on a global level.

```
{{site.iocage_cli_tool}} -d <PRINT_LEVEL> <COMMAND>
```

The print_level `spam` is very verbose but great for debugging.

### Logger Extension

Cases that require manual handling of log entries (for example logging to files or remote services) invite for extension of the Logger class.

```python
class MyLogger(iocage.Logger.Logger):

    def __init__(self, *args, **kwargs) -> None:
       log_file = "/var/log/iocage.log"
       self._log_file_handle = open(log_file)
       super().__init__(*args, **kwargs)

    def _print(self, message: str, level: str, indent: int=0) -> None:
        self._log_file_handle.write(f"{message}\n")

    def __del__(self) -> None:
        self._log_file_handle.close()

jail = iocage.Jail(
    "myjail",
    logger=MyLogger()
)
```
