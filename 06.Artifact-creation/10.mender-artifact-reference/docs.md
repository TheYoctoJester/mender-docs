---
title: mender-artifact reference
taxonomy:
    category: docs
    label: reference
---

The [mender-artifact](https://github.com/mendersoftware/mender-artifact?target=_blank) tool is the primary means of creating and manipulating artifacts for consumption by both the management server as well as the device client. It supports a number of commands that express different actions. This section provides an in-depth documentation of those commands, their respective arguments and parameters. 

### Artifact creation and validation

#### Command `write`

This commands creates and writes an artifact file. It follows the form

`mender-artifact write [module-image|rootfs-image] [arguments]`

where `[module-image|rootfs-image]` declares the type of the artifact. The required and optional `[arguments]` depend on the selected type.

##### Mandatory arguments for all artifact types

| Argument | Description |
| :------- | :---------- |
| `--artifact-name value` or `-n value` | Sets the name to `value` for the resulting artifact. This name will be visible in the management interface. |
| `--device-type value` or `-t value`   | Sets the device type `value` to be compatible with the resulting artifact. Can be given multiple times. |

##### Optional arguments for all artifact types

| Argument | Description |
| :------- | :---------- |
| `--artifact-name-depends value` or `-N value` | Sets the name(s) of the artifact(s) which this update depends upon. |
| `--clears-provides value`                     | Add a `clears_artifact_provides` field to artifact payload. |
| `--compression value`                         | Compression to use for data and header inside the artifact, currently supports: `none`, `gzip`, `lzma`. |
| `--depends KEY:VALUE` or `-d KEY:VALUE`       | Generic `KEY:VALUE` which is added to the `type-info -> artifact_depends` section. Can be given multiple times. |
| `--depends-groups value` or `-G value`        | The group(s) the artifact depends on. |
| `--key value` or `-k value`                   | Full path to the private key that will be used to sign the artifact. |
| `--no-default-clears-provides`                | Do not add any default `clears_artifact_provides` fields to artifact payload. |
| `--no-default-software-version`               | Disable the software version field for compatibility with old clients. |
| `--output-path value` or `-o value`           | Full path to output artifact file. |
| `--provides KEY:VALUE` or `-p KEY:VALUE`      | Generic `KEY:VALUE` which is added to the `type-info -> artifact_provides` section. Can be given multiple times. |
| `--provides-group value` or `-g value`        | The group the artifact provides. |
| `--script value` or `-s value`                | Full path to the state script(s). Can be given multiple times. |
| `--software-filesystem value`                 | If specified, is used instead of rootfs-image. |
| `--software-name value`                       | Name of the key to store the software version: `rootfs-image.NAME.version`, instead of `rootfs-image.PAYLOAD_TYPE.version` |
| `--software-version value`                    | Value for the software version, defaults to the name of the artifact. |
| `--version value, -v value`                   | Version of the artifact. (default: 3) |

##### Artifact type `module-image`: artifact for an update module

The command creates a generic Mender artifact that will be used by an update module. This command is not meant to be used directly, but should rather be wrapped by an update module build command, which prepares all the necessary files and headers for that update module.

###### Required arguments

| Argument | Description |
| :------- | :---------- |
| `--type value` or `-T value` | Type of payload. This is the same as the name of the update module |

###### Optional arguments

| Argument | Description |
| :------- | :---------- |
| `--file FILE` or `-f FILE`                    | Include `FILE` in payload. Can be given more than once. |
| `--meta-data FILE` or `-m FILE`               | The meta-data JSON `FILE` for this payload |


###### Experimental feature: artifact augmentation

The *artifact augmentation* feature is currently under development and considered experimental.

| Argument | Description |
| :------- | :---------- |
| `--augment-depends KEY:VALUE`                 | Generic `KEY:VALUE` which is added to the augmented `type-info -> artifact_depends` section. Can be given multiple times. |
| `--augment-file FILE`                         | Include `FILE` in payload in the augment section. Can be given multiple times. |
| `--augment-meta-data FILE`                    | The meta-data JSON `FILE` for this payload, for the augmented section. |
| `--augment-provides KEY:VALUE`                | Generic `KEY:VALUE` which is added to the augmented `type-info -> artifact_provides` section. Can be given multiple times. |
| `--augment-type value`                        | Type of augmented payload. This is the same as the name of the update module. |

###### Examples

TBD

##### Artifact type `rootfs-image`: artifact containing a rootfs image.

The command creates a Mender artifact that carries a system update to be consumed by the A/B mechanism.

###### Required arguments

| Argument | Description |
| :------- | :---------- |
| `--file FILE` or `-f FILE` | Path of the payload `FILE` or ssh-url to device for system snapshot (e.g. `ssh://user@device:22022`). |

###### Optional arguments

| Argument | Description |
| :------- | :---------- |
| `--gcp-kms-key value`                         | Resource ID of the GCP KMS key that will be used to sign the artifact. |
| `--legacy-rootfs-image-checksum`              | Use the legacy key name rootfs_image_checksum to store the providese checksum to the artifact provides parameters instead of rootfs-image checksum. |
| `--no-checksum-provide`                       | Disable writing the provides checksum to the artifact provides parameters. This is needed in case the targeted devices do not support provides and depends yet. |
| `--no-progress`                               | Suppress the progressbar output. |
| `--ssh-args value` or `-S value`              | Arguments to pass to ssh - only applies when creating artifact from snapshot (i.e. FILE contains 'ssh://' schema) |

###### Examples

TBD

#### Command `validate`

The command validates an existing artifact file. It follows the form

`mender-artifact validate [options] <pathspec>`

where `<pathspec>` is the path to the artifact file that shall be validated.

| Option | Description |
| :----- | :---------- |
| `--gcp-kms-key value`       | Resource ID of the GCP KMS key that will be used to sign the artifact. |
| `--key value` or `-k value` | Full path to the public key that will be used to verify the artifact signature. |

### Artifact inspection

#### Command `read`

The command reads an existing artifact file. It follows the form

`mender-artifact validate [options] <pathspec>`

where `<pathspec>` is the path to the artifact file that shall be validated.

| Option | Description |
| :----- | :---------- |
| `--gcp-kms-key value`       | Resource ID of the GCP KMS key that will be used to sign the artifact. |
| `--key value` or `-k value` | Full path to the public key that will be used to verify the artifact signature. |
| `--no-progress` | Suppress the progressbar output. |

#### Command `dump`

This command dumps various raw files from the artifact. These can be used to create a new Artifact with the same components. It follows the form

`mender-artifact dump [options] <pathspec>`

where `<pathspec>` is the path to the artifact file from which files shall be dumped.

| Option | Description |
| :----- | :---------- |
| `--files value`     | Dump all included files in the first payload into given folder. |
| `--meta-data value` | Dump the contents of the meta-data field in the first payload into given folder. |
| `--print-cmdline`   | Print the command line that can recreate the same Artifact with the components being dumped. If all the components are being dumped, a nearly identical Artifact can be created. Note that timestamps will cause the checksum of the Artifact to be different, and signatures can not be recreated this way. The command line will only use long option names. |
| `--print0-cmdline`  | Same as 'print-cmdline', except that the arguments are separated by a null character (0x00). |
| `--scripts value`   | Dump all included state scripts into given folder. |

### Artifact modification

#### Command `sign`

This command signs an existing artifact. It follows the form

`mender-artifact sign [options] <pathspec>`

where `<pathspec>` is the path to the artifact file that shall be signed.

| Option | Description |
| :----- | :---------- |
| `--force` or `-f`           | Force creating new signature if artifact is already signed. |
| `--gcp-kms-key value`       | Resource ID of the GCP KMS key that will be used to sign the artifact. |
| `--key value` or `-k value` | Full path to the public key that will be used to verify the artifact signature. |
| `--output-path` or `-o`     | Full path to output signed artifact file; if none is provided existing artifact will be replaced with the signed one. |

#### Command `modify`

This command modifies an existing artifact. It follows the form

`mender-artifact modify [options] <pathspec>`

where `<pathspec>` is the path to the artifact file that shall be modified.

| Option | Description |
| :----- | :---------- |
| `--artifact-name value` or `-n value`         | Sets the name to `value` for the resulting artifact. This name will be visible in the management interface. |
| `--artifact-name-depends value` or `-N value` | Sets the name(s) of the artifact(s) which this update depends upon. |
| `--clears-provides value`                     | Add a `clears_artifact_provides` field to artifact payload. |
| `--compression value`                         | Compression to use for data and header inside the artifact, currently supports: `none`, `gzip`, `lzma`. |
| `--delete-clears-provides value`              | Erase one "Clears Provides" filter from the artifact. |
| `--depends KEY:VALUE` or `-d KEY:VALUE`       | Generic `KEY:VALUE` which is added to the `type-info -> artifact_depends` section. Can be given multiple times. |
| `--depends-groups value` or `-G value`        | The group(s) the artifact depends on. |
| `--gcp-kms-key value`                         | Resource ID of the GCP KMS key that will be used to sign the artifact. |
| `--key value` or `-k value`                   | Full path to the private key that will be used to sign the artifact. |
| `--meta-data FILE` or `-m FILE`               | The meta-data JSON `FILE` for this payload |
| `--name value`                                | Deprecated. This is an alias for `--artifact-name` |
| `--provides KEY:VALUE` or `-p KEY:VALUE`      | Generic `KEY:VALUE` which is added to the `type-info -> artifact_provides` section. Can be given multiple times. |
| `--provides-group value` or `-g value`        | The group the artifact provides. |
| `--server-cert value` or `-c value`           | Full path to the certificate file that will be used for validating Mender server by the client. |
| `--server-uri value` or `-u value`            | Mender server URI; the default URI will be replaced with given one. |
| `--tenant-token value` or `-t value`          | Full path to the tenant token that will be injected into modified file. |
| `--verification-key value` or `-v value`      | Full path to the public verification key that is used by the client to verify the artifact. |

#### Command `cp`

Copy from or into an artifact, or sdimg where either the <src> or <dst> has to be of the form [artifact|sdimg]:<pathspec>, <src> can come from stdin in the case that <src> is '-'. The command follows the form:

`cp [options] \<src\> \<dst\>`

| Option | Description |
| :----- | :---------- |
| `--compression value`       | Compression to use for data and header inside the artifact, currently supports: `none`, `gzip`, `lzma`. |
| `--gcp-kms-key value`       | Resource ID of the GCP KMS key that will be used to sign the artifact. |
| `--key value` or `-k value` | Full path to the private key that will be used to sign the artifact. |

#### Command `cat`

Output a file from an artifact to stdout. The command follows the form

`cat [artifact|sdimg|uefiimg]:\<filepath\>`

#### Command `install`

Install a file or directory from the host system into the artifact. The command follows the forms 

`install -m \<permissions\> \<hostfile\> [artifact|sdimg|uefiimg]:\<filepath\>` (for a file)

`install -d [artifact|sdimg|uefiimg]:\<directory\>` (for a directory)

#### Command `rm`

Remove a file or directoy from an artifact. The command follows the form

`rm [options] [artifact|sdimg|uefiimg]:\<filepath\>`

| Option | Description |
| :----- | :---------- |
| `--recursive` or `-r` | Remove directories and their contents recursively. |

### Global

#### Command `help`

Shows a list of commands or help for one command

### Global options

#### `--help`, `-h`

show help

#### `--version`, `-v`

print the version

### Notes

For the commands `write` and `modify`, the `--compression` flag functions as a global option.