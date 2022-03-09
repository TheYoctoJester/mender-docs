---
title: mender-artifact reference
taxonomy:
    category: docs
    label: reference
---

[mender-artifact](https://github.com/mendersoftware/mender-artifact?target=_blank) supports a number of commands that express different actions. This section provides an in-depth documentation of those commands, their respective arguments and parameters. 

### Artifact creation and validation

#### write

Writes artifact file

#### validate

Validates artifact file

### Artifact inspection

#### read

Reads artifact file

#### dump

Dump contents from artifacts

### Artifact modification

#### sign

Signs existing artifact file

#### modify

Modifies image or artifact file

#### cp

cp \<src\> \<dst\>

#### cat

cat [artifact|sdimg|uefiimg]:\<filepath\>

#### install

install -m \<permissions\> \<hostfile\> [artifact|sdimg|uefiimg]:\<filepath\> or install -d [artifact|sdimg|uefiimg]:\<directory\>

#### rm

rm [artifact|sdimg|uefiimg]:\<filepath\>

### Global

#### help

Shows a list of commands or help for one command

### Global options

#### --help, -h

show help

#### --version, -v

print the version

### Notes

For the commands `write` and `modify`, the `--compression` flag functions as a global option.