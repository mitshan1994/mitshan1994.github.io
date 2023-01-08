---
layout: post
title:  "Environment Variables Affecting GCC"
categories: build
---

We can change bash's environment variables to affect gcc, such as adding include path and link path.

### Include Path
`CPATH` can be set to add including path, regardless of what language is using. Colon separated. An empty string in the begin or end will make "current directory ." as one of including directories.

### Link Path
`LIBRARY_PATH` can be set to add link path. It's colon separated.

### Example
We can add these into **.bashrc**:
```bash
export CPATH=/home/mitshan/local/include
export LIBRARY_PATH=/home/mitshan/local/lib
```
