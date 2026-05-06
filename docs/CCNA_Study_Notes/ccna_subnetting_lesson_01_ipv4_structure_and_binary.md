# CCNA Subnetting – Lesson 1
# IPv4 Structure and Binary

## Goal of this lesson

After this lesson you should understand:

```text
1. What an IPv4 address is.
2. Why an IPv4 address has 32 bits.
3. What the four parts of an IP address mean.
4. What an octet is.
5. Why numbers in an IP address are from 0 to 255.
6. How the binary system works.
7. How to convert decimal numbers to binary.
8. How to convert binary numbers to decimal.
9. Why subnetting is based on bits, not only on numbers like 192.168.1.1.
```

---

## 1. What is an IPv4 address?

An IPv4 address is used to identify a device in a network.

Example:

```text
192.168.1.10
```

This address can belong to a computer, laptop, phone, server, router, printer, IP camera, managed switch, or industrial machine.

An IP address works a little bit like a home address. If you want to send a letter, you need a home address. If a computer wants to send data to another device, it needs an IP address.

Example:

```text
A PC wants to open a website.
The PC must send a packet to a server.
The server must know where to send the answer back.
IP addresses are used for that.
```

---

## 2. What does an IPv4 address look like?

An IPv4 address has four parts:

```text
192.168.1.10
```

We can split it like this:

```text
192 . 168 . 1 . 10
```

Each part is separated by a dot. Each part is called an **octet**.

```text
192     . 168     . 1       . 10
octet 1   octet 2   octet 3   octet 4
```

---

## 3. What is an octet?

An octet is a group of 8 bits.

```text
1 octet = 8 bits
```

An IPv4 address has 4 octets.

```text
4 octets × 8 bits = 32 bits
```

That is why we say:

```text
IPv4 address is 32 bits long.
```

---

## 4. What is a bit?

A bit is the smallest unit of information in a computer. A bit can have only two values:

```text
0 or 1
```

A computer works with electrical states. In a simplified way:

```text
0 = off
1 = on
```

So a computer works internally with binary.

---

## 5. Decimal system vs binary system

In normal life we use the decimal system.

```text
Decimal digits: 0 1 2 3 4 5 6 7 8 9
Binary digits:  0 1
```

You see an IP address as decimal:

```text
192.168.1.10
```

But the computer understands it as binary:

```text
11000000.10101000.00000001.00001010
```

---

## 6. Why does one octet range from 0 to 255?

Each octet has 8 bits. Each bit can be 0 or 1.

The smallest value is:

```text
00000000 = 0
```

The largest value is:

```text
11111111 = 255
```

That is why each part of an IPv4 address can be from 0 to 255.

Correct examples:

```text
192.168.1.10
10.0.0.1
172.16.50.200
8.8.8.8
```

Incorrect examples:

```text
192.168.1.300
10.0.0.999
172.16.500.1
```

---

## 7. Bit values in one octet

Each of the 8 bits has a value. From left to right:

```text
128   64   32   16   8   4   2   1
```

This is one of the most important subnetting tables.

If the bit is `1`, you add its value. If the bit is `0`, you do not add its value.

---

## 8. Example: binary to decimal

Binary number:

```text
11000000
```

Write the values above the bits:

```text
128   64   32   16   8   4   2   1
 1     1    0    0   0   0   0   0
```

Add only the values where the bit is `1`:

```text
128 + 64 = 192
```

So:

```text
11000000 = 192
```

---

## 9. Example: 168 to binary

Convert:

```text
168
```

Bit value table:

```text
128   64   32   16   8   4   2   1
```

Does 168 contain 128? Yes, so write `1`.

```text
168 - 128 = 40
```

Does 40 contain 64? No, so write `0`.

Does 40 contain 32? Yes, so write `1`.

```text
40 - 32 = 8
```

Does 8 contain 16? No, so write `0`.

Does 8 contain 8? Yes, so write `1`.

```text
8 - 8 = 0
```

The rest are zeros.

```text
128   64   32   16   8   4   2   1
 1     0    1    0   1   0   0   0
```

Result:

```text
168 = 10101000
```

---

## 10. Example: 10 to binary

Convert:

```text
10
```

10 contains 8, then 2.

```text
10 = 8 + 2
```

So:

```text
128   64   32   16   8   4   2   1
 0     0    0    0   1   0   1   0
```

Result:

```text
10 = 00001010
```

Important: in an IP address octet we write all 8 bits, including leading zeros.

---

## 11. Full IPv4 to binary example

Address:

```text
192.168.1.10
```

Convert each octet separately:

```text
192 = 11000000
168 = 10101000
1   = 00000001
10  = 00001010
```

Full address in binary:

```text
11000000.10101000.00000001.00001010
```

---

## 12. Why binary is important for subnetting

Subnetting works on bits.

When we say:

```text
/24
```

we mean:

```text
the first 24 bits are the network portion
the remaining 8 bits are the host portion
```

Example:

```text
192.168.1.10/24
```

Binary:

```text
11000000.10101000.00000001.00001010
```

Division at `/24`:

```text
Network bits: 11000000.10101000.00000001
Host bits:                                      00001010
```

So:

```text
Network part = 192.168.1
Host part    = 10
```

The device belongs to:

```text
192.168.1.0/24
```

---

## 13. What does `/24` mean exactly?

Address:

```text
192.168.1.10/24
```

means:

```text
IP address: 192.168.1.10
Prefix:     /24
```

Prefix `/24` says:

```text
The first 24 bits are the network part.
```

IPv4 has 32 bits, so:

```text
32 - 24 = 8
```

That means 8 bits remain for hosts.

---

## 14. Network portion and host portion

Every IP address in subnetting is divided into:

```text
network portion
host portion
```

Example:

```text
192.168.1.10/24
```

For `/24`:

```text
192.168.1 = network portion
10        = host portion
```

This simple decimal split works nicely for `/24`. For prefixes such as `/26`, the split does not happen exactly on the dot, so binary is important.

---

## 15. What does subnet mask mean?

A subnet mask tells the device which bits are network bits and which bits are host bits.

Example:

```text
/24 = 255.255.255.0
```

Binary subnet mask:

```text
11111111.11111111.11111111.00000000
```

Meaning:

```text
1 = network bit
0 = host bit
```

---

## 16. Why does 255 mean all ones?

In one octet:

```text
128 64 32 16 8 4 2 1
```

If all bits are `1`:

```text
11111111
```

we calculate:

```text
128 + 64 + 32 + 16 + 8 + 4 + 2 + 1 = 255
```

So:

```text
11111111 = 255
```

---

## 17. Subnet masks you must understand

```text
/8   = 255.0.0.0
/16  = 255.255.0.0
/24  = 255.255.255.0
/25  = 255.255.255.128
/26  = 255.255.255.192
/27  = 255.255.255.224
/28  = 255.255.255.240
/29  = 255.255.255.248
/30  = 255.255.255.252
/32  = 255.255.255.255
```

You should understand where these masks come from, not only memorise them.

---

## 18. Where does `/25 = 255.255.255.128` come from?

`/24` is:

```text
11111111.11111111.11111111.00000000
```

`/25` means one more network bit:

```text
11111111.11111111.11111111.10000000
```

The last octet is:

```text
10000000 = 128
```

So:

```text
/25 = 255.255.255.128
```

---

## 19. Where does `/26 = 255.255.255.192` come from?

`/26` means 26 network bits. In the last octet we have 2 network bits:

```text
11000000
```

Calculate:

```text
128 + 64 = 192
```

So:

```text
/26 = 255.255.255.192
```

---

## 20. Where does `/27 = 255.255.255.224` come from?

`/27` means 3 network bits in the last octet:

```text
11100000
```

Calculate:

```text
128 + 64 + 32 = 224
```

So:

```text
/27 = 255.255.255.224
```

---

## 21. Where does `/28 = 255.255.255.240` come from?

`/28` means 4 network bits in the last octet:

```text
11110000
```

Calculate:

```text
128 + 64 + 32 + 16 = 240
```

So:

```text
/28 = 255.255.255.240
```

---

## 22. Last-octet table

```text
Binary       Decimal   Prefix
10000000     128       /25
11000000     192       /26
11100000     224       /27
11110000     240       /28
11111000     248       /29
11111100     252       /30
11111110     254       /31
11111111     255       /32
```

---

## 23. Network and broadcast addresses

If all host bits are `0`, we have the network address.

Example `/24`:

```text
192.168.1.0
```

If all host bits are `1`, we have the broadcast address.

Example `/24`:

```text
192.168.1.255
```

You do not assign network or broadcast addresses to normal devices.

---

## 24. Why do we subtract 2?

For `/24`, we have 8 host bits.

Total combinations:

```text
2^8 = 256
```

But:

```text
00000000 = network address
11111111 = broadcast address
```

So:

```text
256 - 2 = 254 usable hosts
```

Formula:

```text
usable hosts = 2^host_bits - 2
```

---

## 25. Important `/26` example

Address:

```text
192.168.1.70/26
```

Mask `/26`:

```text
255.255.255.192
```

Binary mask:

```text
11111111.11111111.11111111.11000000
```

This means:

```text
26 network bits
6 host bits
```

`/26` divides the last octet into blocks of 64:

```text
0-63
64-127
128-191
192-255
```

The address `70` is inside:

```text
64-127
```

So:

```text
Network:   192.168.1.64/26
Broadcast: 192.168.1.127
Usable:    192.168.1.65 - 192.168.1.126
```

---

## 26. Why does block size work?

For `/26`, mask is:

```text
255.255.255.192
```

Last octet of the mask:

```text
192
```

Block size:

```text
256 - 192 = 64
```

So networks begin every 64:

```text
192.168.1.0
192.168.1.64
192.168.1.128
192.168.1.192
```

This comes from binary. Mask `/26` leaves 6 host bits:

```text
2^6 = 64 total addresses per subnet
```

---

## 27. What to remember after Lesson 1

```text
IPv4 address = 32 bits
IPv4 address = 4 octets
1 octet = 8 bits
1 octet range = 0-255
Binary uses only 0 and 1
Decimal uses 0-9
Subnetting works on bits
/24 means 24 network bits
Host bits = 32 - prefix
Subnet mask uses 1s for network bits and 0s for host bits
All host bits 0 = network address
All host bits 1 = broadcast address
Usable hosts = 2^host_bits - 2
```

---

## 28. Mini homework

### Part A — Decimal to binary

Convert to binary:

```text
1. 192
2. 168
3. 10
4. 1
5. 255
6. 0
7. 128
8. 224
9. 240
10. 252
```

### Part B — Binary to decimal

Convert to decimal:

```text
1. 11000000
2. 10101000
3. 00000001
4. 00001010
5. 11111111
6. 10000000
7. 11100000
8. 11110000
9. 11111100
10. 00000000
```

### Part C — Prefix understanding

```text
1. How many host bits does /24 have?
2. How many host bits does /26 have?
3. How many host bits does /27 have?
4. How many network bits does /30 have?
5. How many bits does IPv4 have in total?
```

---

## 29. Answers

### Part A

```text
1. 192 = 11000000
2. 168 = 10101000
3. 10  = 00001010
4. 1   = 00000001
5. 255 = 11111111
6. 0   = 00000000
7. 128 = 10000000
8. 224 = 11100000
9. 240 = 11110000
10. 252 = 11111100
```

### Part B

```text
1. 11000000 = 192
2. 10101000 = 168
3. 00000001 = 1
4. 00001010 = 10
5. 11111111 = 255
6. 10000000 = 128
7. 11100000 = 224
8. 11110000 = 240
9. 11111100 = 252
10. 00000000 = 0
```

### Part C

```text
1. /24 has 8 host bits
2. /26 has 6 host bits
3. /27 has 5 host bits
4. /30 has 30 network bits
5. IPv4 has 32 bits
```

---

## 30. Main idea from Lesson 1

Do not learn subnetting only as a table.

Learn it like this:

```text
IP address is binary.
Subnet mask is binary.
Prefix tells how many bits belong to the network.
The remaining bits belong to hosts.
```

If you understand this, then `/24`, `/26`, `/20`, VLSM, wildcard masks, and route summarization will become much easier.
