# Version Number Scheme

Version numbering is adapted from [Semver 2.0.0 (semver.org)](https://semver.org/).

Version numbers consist of four parts in order.

* Channel: The channel is a release channel and determines the interpretation of the subsequent version number. Channels are ASCII alphabetic `[a-zA-Z]`. Channel determines the interpretation of the rest of the version number.
* Version: A dot separated list of identifiers.  Each identifier is either numeric `[0-9]` or alphanumeric `[a-zA-Z0-9]`. Numeric identifiers must not have leading zeros.
* Pre-release/Post-release: A dot separated list of identifiers. Identifiers MUST comprise only ASCII alphanumerics and hyphen `[0-9A-Za-z-]`.
* Hash: A unique identifier for the version. The identifier MUST comprise only ASCII alphanumerics and hyphen `[0-9A-Za-z-]`.
* Metadata:

Version Ordering:

* No ordering is defined between different release channels.
* A dash separates the channel and version. For the default channel, the channel name is dropped.
* Versions are ordered by parts
* Pre-release is separated from the version by `-`. Post-release by `+`. Both are unstable. Pre-release versions are before the main version. Post-release are after.
* The hash is separated by `#` if a hash is present, two versions are equal only if there hashes are equal. Hashes imply no version ordering.
* Metadata is separated by `!` and isn't considered as part of ordering.

The channel determines the interpretation and allowed format of version numbers. Default channel always Semver.

Channels:

* nightly - date versioning
* dev - hash versioning

Schemes:

* date: `<year>.<month>.<day>.<release-number>`
* hash: only provide a hash

Metadata is used for indicating things like what platform something is built for. Note: the version number should always distinguish x86 form x64 versions.
