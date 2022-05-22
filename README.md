# Lotus 1-2-3 for Linux

This is a native port of Lotus 1-2-3 version 3 to Linux. This is possible
because the official Lotus 1-2-3 for UNIX port used a technique called [partial
linking](https://sourceware.org/binutils/docs-2.38/ld/Options.html#:~:text=This%20is%20often%20called%20partial%20linking.) to workaround a technical limitation in early UNIX systems. Surprisingly, that can be used to modify it to support new platforms.

There's an article documenting how this is possible
[here](https://lock.cmpxchg8b.com/linux123.html).

![Lotus 1-2-3 for Linux](https://lock.cmpxchg8b.com/img/123linux.png)

## Building

### With Docker/Podman

If you have [Docker](https://www.docker.com/) or [Podman](https://podman.io/) installed you can run
```sh
./run-from-container.sh 20
```

And that script will automatically build Lotus 1-2-3 within a Ubuntu 20 container and then run it in another container.
Other Ubuntu versions are supported (launch `./run-from-container.sh` without arguments to see which).

You can also build the `123` binary only by using:

```sh
./build-from-host.sh 20
```

But the produced binary will not work if your host system libraries differ from the container.

### Dependencies

First, you need a version of binutils that is compiled with `coff-i386` target
support. You can check like this:

```
$ objdump --info | grep coff-i386
```

> Note: Most distributions do not enable this for some reason.

Secondly, you need a copy of Lotus 1-2-3 for UNIX, you can download it
[here](https://archive.org/details/123-unix). Just place the raw disk images in 
the build directory and run `extract.sh`.

Finally, just run `make`.

#### Packages

The following packages are required

| Ubuntu              | Fedora              |
| ------------------- | ------------------- |
| build-essential     | glibc-devel.i686    |
| gcc-multilib        | libgcc.i686         |
| lib32ncurses-dev    | ncurses-static.i686 |

### Binutils

Unfortunately, most distributions do not enable `coff-i386` support in binutils.

It's very easy to enable it yourself, download [binutils](https://www.gnu.org/software/binutils/) and configure it with `--enable-targets=all`.

There's no need to install it, just build it with `make`.

When that's complete, copy `objcopy` and `objdump` from the `binutils` directory, and `ld-new` from the `ld` directory to `123elf` directory.

> Important: Remember to rename `ld-new` to `ld`.

The Makefile should automatically use the new binaries, and continue to build.

## Running

Copy the file `l123set.cf` to `~/.l123set`, and run `./123`.

### Getting Started

- There is a man page in `./root/lotus/man/man1/123.1`, and a full manual available online [here](https://archive.org/details/lotus-1-2-3-release-3.1-reference/Lotus%201-2-3%20Release%203.1%20-%20Tutorial).

> Note: You use use the `/` key to open the 123 menu!

If you've used any spreadsheet before, you should be able to get started
quickly. Functions use `@` instead of `=`, but the common functions like
`@SUM`, `@AVG`, `@INDEX`, and even `@HLOOKUP` all work as you would expect.

## Bugs

- The keyboard map seems to be incomplete - probably a termcap issue?
- Graphs don't work yet (I'm working on it!).
- Printing doesn't work yet.
- File an issue if you notice something, there are probably lots of minor issues that can be fixed!
