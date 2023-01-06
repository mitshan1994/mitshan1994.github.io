---
layout: post
title:  "Boost Building"
categories: build
---

Some keynotes on building boost.

### On Ubuntu
```bash
# Install prefix can be changed.
./bootstrap.sh --prefix=/home/mitshan/local

# Build after configuring, and install it.
# Static release library is used.
./b2 --prefix=/home/mitshan/local variant=release link=static threading=multi runtime-link=shared install
```
