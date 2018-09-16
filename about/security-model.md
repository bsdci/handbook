libiocage provides secure compartments using the FreeBSD jail feature.
Therefore the project follows an uncompromising approach of protecting the host system from any third party content downloaded or coming from within a jail.

#### Release Download

Releases of both supported distributions, FreeBSD and HardenedBSD, are downloaded from the according content delivery network via encrypted HTTPS connection.
Only after the hashes of the downloaded archives are verified, libiocage begins extracting them.
At first the assets are verified to only contain relative paths before they are finally extracted to their target release datasets, so that the host is protected even if the distribution content delivery server would contain compromised files.

#### Release Updates

The update process of releases is splitted in two stages. The first stage executes freebsd-update (respectively hbsd-update) on the host to obtain patches from the remote.
In the second stage libiocage creates a temporary jail that mount the fetched updates read-only, so that updates can be applied onto a minimal jail without network or any privileged permissions.
The latter allows updating non-basejails without introducing risk to the host system.


#### Paths

Whenever libioage needs to access paths within a jail (for instance to create fstab mountpoints), the absolute paths are verified to be ancestors of the jails root dataset.
This measure prevents break out scenarios where the host is tricked to follow symlinks.
Because the check occurs before executing such operations jails must not run for the actual command execution, so that paths can not be modified during runtime after the safety check.

#### External Commands

Not every internal features of libiocage uses Python modules that bind to system commands, but often execute external commands using Popen.
The input such commands are executed with is quoted using shlex to mitigate risk of command injection from user provided data.
Properly escaped arguments are especially important when libiocage is used when external parties may alter a jails configuration (for example in shared-hosting scenarios).