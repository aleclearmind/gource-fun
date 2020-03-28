# Overview

This is a collection of experiments with unorthodox usages of `gource` for
visualization.  All of this is made possible thanks to the [gource custom log
format](https://github.com/acaudwell/Gource/wiki/Custom-Log-Format).

## `btrfs-gource`

Render in `gource` the files changed since a certain BTRFS commit.
The tools is based on `btrfs subvolume find-new`.

Example:

```
./btrfs-gource | ./gource-view
./btrfs-gource 1 | ./gource-view
```

## `trace-gource`

Uses [`bpftrace`](https://github.com/iovisor/bpftrace) to monitor activity on
file descriptors and visualize it in `gource`.  It currently monitors `open`,
`openat`, `read`, `write`, `close`, `socket`, `bind`, `connect`, `accept`,
`recvfrom` and `sendto`.

It runs the given command in a new `cgroup` so that we can easily monitor all
the subprocesses.

Example:

```
./trace-gource "touch testfile" 2>/dev/null | ./gource-view
./trace-gource "find / -name '*.xml' -exec xml_pp {} \;" 2>/dev/null | ./gource-view
```

## Implementation notes

* Note that due to a [`bpftrace`
  limitation](https://github.com/iovisor/bpftrace/issues/305) long paths will be
  cut.
* `bpftrace` does not seem to support custom functions, or macros. Therefore we
  employed the C preprocessor so that we can use C-style macros.

## Creating a video

Replace `./gource-view` with `./gource-mp4 $OUTPUT_FILE`.
