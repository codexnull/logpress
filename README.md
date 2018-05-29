# logpress

logpress is a utility to compress log files.

The program is implemented in a single file with (AFAIK) no dependencies other than bash. There is no need to install or configure anything to use it.

### Usage

    logpress [opts] <dir> [dir2 dir2 ...]

### Options

The options below are supported by the program.

* `-h` `--help`

  Output a summary of usage and options and exit.

* `-v` `--version`

  Output the version number, currently v1.0.0, and exit.

* `-n` `--dry-run`

  Don't compress anything, just report what files *would* be compressed.

* `-p` `--pidfile`

  Specify path to write PID in. Default is `/var/run/logpress.pid`. A path of
  `-` is interpreted as STDOUT.

* `-l` `--logfile`

  Specify path to write log in. Default is `/var/log/logpress.log` (or STDOUT if `--dry-run`). A path of `-` is interpreted as STDOUT.

* `-sN` `--min-size=N`

  Specify minimum size of log files to compress. Default is 4 KiB. File sizes 0-1023 are special.

  * A file size of 0 means all log files, even empty ones. Not recommended since this may actually *increase* disk usage. Empty files usually consume only an inode. Compressing them would make them non-zero and thus consume at least one block.
  * A file size of 1 means all non-empty log files. This may not yield any additional savings since files can't consume less that one unit of the file system's minimum allocation size.
  * A file size of 2-1023 means log files of at least that many KiB.
  * A file size of >= 1024 means log files of at least that many bytes.

  That is, `--min-size=8` and `--min-size=8192` both mean "compress log files that are 8 KiB or larger."

* `-aN` `--min-age=N`

  Specify minimum age, in days, of log files to compress. Default is 30 days.
  An age of 0 to compress files regardless of age is not recommended as it may compress files that are still being written.

### Description

This program simply descends into the directory or directories specified (and their subdirectories, if any), looks for files which have names ending in '.log' and satisfying the size and age criteria, and compresses them. Files are compressed using low priority in order to have minimal impact on concurrent activity. By default, there is no output and activity is logged under `/var/log`, making the program well suited to being run automatically under cron.

Files are compressed with gzip. An option may be added in the future to compress using a different algorithm (e.g. xz, bzip2, etc).

### Disclaimer

Copyright (C) 2015 by Javier Alvarado.

This program was written for personal use by the author. Permission is hereby granted to study, use, copy, and/or modify for non-commercial use.

***THE SOFTWARE IS PROVIDED "AS IS" AND WITHOUT WARRANTY OF ANY KIND. THE AUTHOR IS IN NO WAY LIABLE FOR ANY DAMAGES OR LOSS OF DATA.***
