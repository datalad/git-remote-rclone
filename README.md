# Git remote helper for rclone-supported services

[![GitHub release](https://img.shields.io/github/release/datalad/git-remote-rclone.svg)](https://GitHub.com/datalad/git-remote-rclone/releases/) [![PyPI version fury.io](https://badge.fury.io/py/git-remote-rclone.svg)](https://pypi.python.org/pypi/git-remote-rclone)

This is a [Git remote helper](https://git-scm.com/docs/git-remote-helpers) for
(any?) service supported by [rclone](https://rclone.org). In other words, this
helper makes it possible to push and pull to a large number of storage services
with no native Git support.

## Usage

Visit the [rclone website](https://rclone.org) and follow the instructions for
installation of `rclone`, and configuration of access to the desired
service(s). Install this package via pip (`pip install git-remote-rclone`), or
place the `git-remote-rclone` executable provided here into the system path,
such that Git can find it. Now it is possible to use URLs like
`rclone://<remote>/<path-on-remote>` as push and pull URLs for Git.

For example, if access to a DropBox account has been configured as an rclone-remote
named `mydropbox`, the URL `rclone://mydropbox/myrepository` identifies a remote
in a directory `myrepository/` under this DropBox account.

## Technical details

`git-remote-rclone` is implemented in Python 3. In addition to a standard
Python installation, it requires [7-Zip](https://www.7-zip.org) to be
available. It has been tested with Python 3.7, but should work from Python 3.5
onward.

At the remote end, `git-remote-rclone` maintains a directory with two files:

- `refs`: a small text file listing the refs provided by the remote
- `repo.7z`: a 7-Zip archive of a bare Git repository

When interacting with a remote, `git-remote-rclone` obtains and extracts a copy
of the remote repository archive (placed at `.git/rclone/<remote-name>` in the
local Git repository). All Git operations are performed locally. Whenever the
state of the mirror repository has changed, it is compacted to minimize transfer
size and uploaded to the remote storage service. Likewise, the remote storage
service is checked for updates before the local mirror repository is updated.

`git-remote-rclone` aims to minimize API usage of remote storage services. Per
invocation, it only queries for the state of a remote repository archive
(checksum), downloads two files (if needed), and uploads two files (if needed,
and on push only).

### Tested with

- `rclone` 1.50.2
- Google Drive
- DropBox

## Limitations

At the moment no locking is performed that would prevent simultaneous
(conflicting) updates from two end points. But this should be straightforward
to add support for.

## Support

All bugs, concerns and enhancement requests for this software can be submitted here:
https://github.com/datalad/git-remote-rclone/issues

## Acknowledgements

This development was supported by European Union’s Horizon 2020 research and
innovation programme under grant agreement [VirtualBrainCloud
(H2020-EU.3.1.5.3, grant no.
826421)](https://cordis.europa.eu/project/id/826421).
