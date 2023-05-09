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

### Bit Order in Network Protocols
When network byte order is specified in protocols, and octal represents a numeric quantity, the left most bit in the diagram is the most significant bit. In the following, label 0 is the most significant bit, and it represents value 170:
```
 0 1 2 3 4 5 6 7
+-+-+-+-+-+-+-+-+
|1 0 1 0 1 0 1 0|
+-+-+-+-+-+-+-+-+
```

## Techniques
We can remember the differences of big/little endianness byte/bit order like this:
* When in big endianness, things go from left to right (lower address to higher).
* When in little endianness, thing go from right to left (lower address to higher).
Under both, we human should read decimal representation naturally, with most significant number in the left and least significant number in the right.
