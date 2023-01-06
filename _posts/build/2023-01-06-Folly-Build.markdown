---
layout: post
title:  "Folly Building"
categories: build
---

This is a personal note on building **folly** library.

```bash
cd folly
./build.sh --allow-system-packages --scratch-path=/home/mitshan/libraries/folly_scratch

# --clean can be added to clean before building. If some error happens in installing stage of some dependent libraries, remove this and try again.
# --num-jobs N can be added to accelerate compiling speed. N is normally the core count.
```

After building, miscellaneous installed libraries/bins will be put into the scratch path (also the build files, download files). You can copy/paste them to your local include/lib directories.

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

### Some Notes on my case of compiling
* Only `fmt` library is donwloaded and compiled, all others uses system libraires downloaded/installed using `apt`(including boost).

#### Boost Library
When I compiling folly, `boost` is installed through `apt`. So, when I using my built boost library during linking to `folly`, strange errors occur. This is due to different versions of boost used during `folly` compiling, and my program linking to `folly` library.

So I removed my boost library, just using the system one, to solve this problem.

But shared version of boost libraries are linked by default, this may be inconvinient since final executable depends on shared boost libraries. So we can link the static version of boost libraries. I searched `folly`'s CMakeLists.txt, and conclude the following used boost libraries:
```
libboost_context.a
libboost_filesystem.a
libboost_program_options.a
libboost_regex.a
libboost_system.a
libboost_thread.a
```

These can be tried if linking fails due to undefined reference of boost related symbols.

### Questions
* If I install libfmt using `apt install libfmt-dev`, together with option `--allow-system-packages` in build scripts, do i have to manually install folly-compiled version of libfmt? Since `libfmt-dev` is not installed as a system dependency in `getdeps.py` script.
