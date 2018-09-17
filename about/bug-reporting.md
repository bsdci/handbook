Issues and Bugs of iocage are tracked on GitHub among the source repositories. 
A direct relation between sources and bug resolution allows us to comprehensively inform about reaons behind changes, which is important to prevent future regressions.

At the time of writing this document, no automated unit- or integration test exist that would allow the project to verify the absence of previously experienced issues. Our aim is to establish proper quality assurance measures that integrate with GitHub Pull-Requests in the recent future.

### How to detect bugs?

As general rule of thumb libiocage will stack-trace on unknown problems. As soon as such occasions are spotted, it is the right moment to speak up in the GitHub Issues of the according project.

In case of undefined behavior, that does not result in such stack-traces, the discussion on GitHub as much appreciated as well.

### What information is required to resolve issues?

GitHub lacks the ability to link an issue to a specific commit. Although it makes sense to upgrade the failing revision to the latest available commit of the `master` branch, mentioning the affected commit SHA sum is always a great help. Because iocage supports different distributions and their releases, information on the host system supports our ability to reproduce and understand the issue.

A bug report should contain at least the following information:

- Commit Hash of the affected revision
- Host Info
  - `uname -KU`
  - `uname -a`
- Copy of the Stack-Trace message
- List of iocage ZFS datasets
  - `zfs list -r <IOCAGE_SOURCE_DATASET>`
- The affected Jail configuration stored in `/iocage/jails/<JAIL_NAME>/config.json` (or legacy storage formats)

### Security and Incident Response

The iocage project highly values its users security which is why we encourage our contributors to file vulnerability issues on a non-public channel until a reasonable precautions have been taken. Pull-Request that introduce mitigations against flaws, that can have a security impact, will contain details on the vulnerability and credit the person who has reported the issue in first place.

User can stay informed by monitoring [GitHub Issues and Pull-Requests tagged with security label](https://github.com/iocage/libiocage/issues?q=label%3Asecurity).