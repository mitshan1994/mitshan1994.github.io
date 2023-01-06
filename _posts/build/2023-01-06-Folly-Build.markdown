---
layout: post
title:  "Folly Building"
categories: build
---

This is a personal note on building **folly** library.

```bash
cd folly
./build.sh --install-prefix=/home/mitshan/local --allow-system-packages
```

After building, miscellaneous installed libraries/bins will be put into directory (which make the directory messy). It's better to specify an empty directory to install first.

Many dependent libraries are installed to system path, but some not, which I didn't figure out why. For example, **libfmt** is not installed to system path, so I need to manually install it to my local include/lib directories.

### Linking
When compiling program which links to `libfolly`, some extra libraries also need to be linked to. Some of them:
```
libfolly.a
libfmt.a
libdouble-conversion.a
libglog.a
libgflags.a
```

I specify these libraries as static ones, so deprecating the compiler/linker to link the dynamic ones.

I think the reason I need to link to these libraries may be one of the reasons:
* folly links to these libraries when building, but doesn't pack their object files together.
* header files are dependent.

### Questions
* If I install libfmt using `apt install libfmt-dev`, together with option `--allow-system-packages` in build scripts, do i have to manually install folly-compiled version of libfmt? Since `libfmt-dev` is not installed as a system dependency in `getdeps.py` script.
