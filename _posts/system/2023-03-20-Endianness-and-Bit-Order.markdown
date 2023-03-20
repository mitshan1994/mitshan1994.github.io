---
layout: post
title:  "Endianness and Bit Order"
categories: system
---

## Bit Order
On little endianness system, consider the following struct:
```
struct OneByte {
	unsigned char
		x0 : 0,
		x1 : 1,
		x2 : 2,
		x3 : 3,
		x4 : 4,
		x5 : 5,
		x6 : 6,
		x7 : 7
}
```
Position 0 (`x0`) is the least significant bit, while position 7 (`x7`) is the most significant bit.

Consider the IP header struct:
```
// Reference: https://github.com/freebsd/freebsd-src/blob/master/sys/netinet/ip.h

struct ip {
#if BYTE_ORDER == LITTLE_ENDIAN
	u_char	ip_hl:4,		/* header length */
		ip_v:4;			/* version */
#endif
#if BYTE_ORDER == BIG_ENDIAN
	u_char	ip_v:4,			/* version */
		ip_hl:4;		/* header length */
#endif
```
And the ip header format is:
![ip_header_pic](/assets/system/ip_header_format.png)


