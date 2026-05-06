# CCNA Subnetting – Lesson 3
# CIDR Notation and Subnet Masks

## Goal of this lesson

After this lesson you should understand:

```text
1. What CIDR notation means.
2. What the slash prefix means, for example /24, /26, /20.
3. What a subnet mask is.
4. How CIDR and subnet masks describe the same thing.
5. How to calculate host bits.
6. How to calculate usable hosts.
7. How to understand /8, /16, /24, /25, /26, /27, /28, /29, /30.
8. Why subnet masks use continuous 1s followed by continuous 0s.
9. How to identify invalid subnet masks.
10. Why the prefix is more important than the old IP address class.
```

---

## 1. What is CIDR?

CIDR means:

```text
Classless Inter-Domain Routing
```

In simple words, CIDR is the modern way of writing network size.

Example:

```text
192.168.1.0/24
```

The `/24` part is CIDR notation.

It tells us:

```text
The first 24 bits are the network part.
The remaining bits are the host part.
```

IPv4 has 32 bits total.

So:

```text
32 - 24 = 8 host bits
```

That means `/24` has 8 bits left for devices.

---

## 2. Why is CIDR called “classless”?

In old classful addressing, the first octet decided the default mask.

Example:

```text
Class A = /8
Class B = /16
Class C = /24
```

But CIDR does not care about the old class default.

Example:

```text
192.168.1.0/27
```

The first octet `192` means this address is from the old Class C range.

But the real network size is not `/24`.

The real network size is:

```text
/27
```

So CIDR means:

```text
Use the prefix that is written.
Do not automatically use the classful default mask.
```

This is very important for CCNA.

---

## 3. What does the slash prefix mean?

The slash prefix tells us how many bits belong to the network.

Example:

```text
/24
```

means:

```text
24 network bits
```

IPv4 has 32 bits.

So:

```text
32 - 24 = 8 host bits
```

Another example:

```text
/26
```

means:

```text
26 network bits
32 - 26 = 6 host bits
```

Another example:

```text
/20
```

means:

```text
20 network bits
32 - 20 = 12 host bits
```

The formula is:

```text
host bits = 32 - prefix
```

---

## 4. What is a subnet mask?

A subnet mask tells the device which bits are network bits and which bits are host bits.

In binary:

```text
1 = network bit
0 = host bit
```

Example:

```text
/24
```

Subnet mask in binary:

```text
11111111.11111111.11111111.00000000
```

Subnet mask in decimal:

```text
255.255.255.0
```

So:

```text
/24 = 255.255.255.0
```

---

## 5. CIDR and subnet mask are two ways of saying the same thing

These two mean the same:

```text
192.168.1.0/24
192.168.1.0 255.255.255.0
```

These two also mean the same:

```text
192.168.1.0/26
192.168.1.0 255.255.255.192
```

And these two also mean the same:

```text
172.16.32.0/20
172.16.32.0 255.255.240.0
```

CIDR is shorter.

Subnet mask is the full decimal mask.

Cisco questions may use either format.

You must be comfortable with both.

---

## 6. Why does a subnet mask use 1s and 0s?

A subnet mask separates the address into two parts:

```text
network part
host part
```

The 1s mark the network part.

The 0s mark the host part.

Example:

```text
IP address:   192.168.1.10/24
Mask binary:  11111111.11111111.11111111.00000000
```

This means:

```text
First 24 bits are network.
Last 8 bits are host.
```

---

## 7. The most important binary table

One octet has 8 bits.

The values are:

```text
128 64 32 16 8 4 2 1
```

If all bits are 1:

```text
11111111
```

Calculation:

```text
128 + 64 + 32 + 16 + 8 + 4 + 2 + 1 = 255
```

So:

```text
11111111 = 255
```

If all bits are 0:

```text
00000000 = 0
```

This is why subnet masks contain numbers like:

```text
255
0
128
192
224
240
248
252
254
```

---

## 8. Prefix to subnet mask table

This table is essential for CCNA.

```text
/8   = 255.0.0.0
/9   = 255.128.0.0
/10  = 255.192.0.0
/11  = 255.224.0.0
/12  = 255.240.0.0
/13  = 255.248.0.0
/14  = 255.252.0.0
/15  = 255.254.0.0
/16  = 255.255.0.0

/17  = 255.255.128.0
/18  = 255.255.192.0
/19  = 255.255.224.0
/20  = 255.255.240.0
/21  = 255.255.248.0
/22  = 255.255.252.0
/23  = 255.255.254.0
/24  = 255.255.255.0

/25  = 255.255.255.128
/26  = 255.255.255.192
/27  = 255.255.255.224
/28  = 255.255.255.240
/29  = 255.255.255.248
/30  = 255.255.255.252
/31  = 255.255.255.254
/32  = 255.255.255.255
```

At the beginning, do not try to memorise the whole table at once.

First understand the pattern.

---

## 9. Pattern inside subnet masks

Look at the changing octet.

Possible subnet mask octet values are:

```text
0
128
192
224
240
248
252
254
255
```

These values come from continuous 1s:

```text
00000000 = 0
10000000 = 128
11000000 = 192
11100000 = 224
11110000 = 240
11111000 = 248
11111100 = 252
11111110 = 254
11111111 = 255
```

Subnet masks must have continuous 1s followed by continuous 0s.

Correct:

```text
11110000 = 240
```

Incorrect:

```text
10110000
```

Why incorrect?

Because the 1s are not continuous.

A valid subnet mask cannot have 1s and 0s mixed randomly.

---

## 10. Valid and invalid subnet masks

Valid subnet masks:

```text
255.0.0.0
255.255.0.0
255.255.255.0
255.255.255.128
255.255.255.192
255.255.255.224
255.255.255.240
255.255.255.248
255.255.255.252
```

Invalid subnet masks:

```text
255.0.255.0
255.255.10.0
255.255.255.250
255.255.192.128
255.255.255.127
```

Why are they invalid?

Because subnet masks must be:

```text
all 1s first, then all 0s
```

Example invalid mask:

```text
255.255.255.250
```

Last octet 250 in binary:

```text
11111010
```

The 1s and 0s are mixed.

So it is not a valid subnet mask.

---

## 11. Host bits calculation

The most important formula:

```text
host bits = 32 - prefix
```

Examples:

```text
/24 -> 32 - 24 = 8 host bits
/25 -> 32 - 25 = 7 host bits
/26 -> 32 - 26 = 6 host bits
/27 -> 32 - 27 = 5 host bits
/28 -> 32 - 28 = 4 host bits
/29 -> 32 - 29 = 3 host bits
/30 -> 32 - 30 = 2 host bits
```

This tells you how many bits remain for devices inside the subnet.

---

## 12. Usable host calculation

The formula is:

```text
usable hosts = 2^host_bits - 2
```

Why minus 2?

Because in normal IPv4 subnets:

```text
all host bits 0 = network address
all host bits 1 = broadcast address
```

These two addresses cannot be assigned to normal hosts.

Example:

```text
/24
```

Host bits:

```text
32 - 24 = 8
```

Usable hosts:

```text
2^8 - 2 = 256 - 2 = 254
```

---

## 13. Host calculation table

```text
/24 = 8 host bits  = 2^8 - 2  = 254 usable hosts
/25 = 7 host bits  = 2^7 - 2  = 126 usable hosts
/26 = 6 host bits  = 2^6 - 2  = 62 usable hosts
/27 = 5 host bits  = 2^5 - 2  = 30 usable hosts
/28 = 4 host bits  = 2^4 - 2  = 14 usable hosts
/29 = 3 host bits  = 2^3 - 2  = 6 usable hosts
/30 = 2 host bits  = 2^2 - 2  = 2 usable hosts
```

For CCNA, this table should become automatic.

---

## 14. What about /31 and /32?

Traditional host formula gives:

```text
/31 -> 1 host bit  -> 2^1 - 2 = 0 usable hosts
/32 -> 0 host bits -> 2^0 - 2 = -1, which does not make practical sense
```

But modern networking treats them specially.

```text
/31 = used on point-to-point links
/32 = single host route
```

For beginner CCNA subnetting exercises, `/30` is the classic point-to-point subnet with 2 usable addresses.

But you should recognise:

```text
/32 = one exact IP address
```

Example:

```text
192.168.1.10/32
```

means exactly one host address:

```text
192.168.1.10
```

---

## 15. Block size

Block size tells us how subnet ranges increase.

For masks that do not end on 255 or 0, use:

```text
block size = 256 - interesting octet
```

The “interesting octet” is the octet where the mask is not 255 and not 0.

Example:

```text
/26 = 255.255.255.192
```

Interesting octet:

```text
192
```

Block size:

```text
256 - 192 = 64
```

So the networks increase by 64 in the last octet:

```text
192.168.1.0
192.168.1.64
192.168.1.128
192.168.1.192
```

---

## 16. Another block size example: /27

```text
/27 = 255.255.255.224
```

Interesting octet:

```text
224
```

Block size:

```text
256 - 224 = 32
```

Networks:

```text
192.168.1.0
192.168.1.32
192.168.1.64
192.168.1.96
192.168.1.128
192.168.1.160
192.168.1.192
192.168.1.224
```

Each subnet has 32 total addresses.

Usable hosts:

```text
32 - 2 = 30
```

This matches:

```text
2^5 - 2 = 30
```

because `/27` has 5 host bits.

---

## 17. Example with /20

This is important because CCNA does not only ask about /24 to /30.

Example:

```text
172.16.35.90/20
```

First, find the subnet mask:

```text
/20 = 255.255.240.0
```

Interesting octet:

```text
third octet = 240
```

Block size:

```text
256 - 240 = 16
```

So networks increase by 16 in the third octet:

```text
172.16.0.0
172.16.16.0
172.16.32.0
172.16.48.0
172.16.64.0
...
```

The third octet of the IP is:

```text
35
```

35 falls into the range:

```text
32 - 47
```

So the network is:

```text
172.16.32.0/20
```

Broadcast is the last address before the next network:

```text
Next network: 172.16.48.0
Broadcast:    172.16.47.255
```

Usable range:

```text
172.16.32.1 - 172.16.47.254
```

---

## 18. Example with /22

Address:

```text
10.10.14.100/22
```

Mask:

```text
/22 = 255.255.252.0
```

Interesting octet:

```text
third octet = 252
```

Block size:

```text
256 - 252 = 4
```

Networks increase by 4 in the third octet:

```text
10.10.0.0
10.10.4.0
10.10.8.0
10.10.12.0
10.10.16.0
...
```

The third octet of the IP is:

```text
14
```

14 falls into:

```text
12 - 15
```

So the network is:

```text
10.10.12.0/22
```

Next network:

```text
10.10.16.0
```

Broadcast:

```text
10.10.15.255
```

Usable range:

```text
10.10.12.1 - 10.10.15.254
```

---

## 19. CIDR is stronger than classful addressing

Address:

```text
10.10.10.10/24
```

Old class:

```text
Class A
```

Class A default mask:

```text
/8
```

But CIDR prefix says:

```text
/24
```

So the real network is:

```text
10.10.10.0/24
```

Not:

```text
10.0.0.0/8
```

This is a very common misunderstanding.

Always use the prefix or mask given in the question.

---

## 20. What happens if no mask is given?

In real modern networking, you should always specify a mask or prefix.

In old classful questions, if no mask is given, you may be expected to use the classful default.

Example:

```text
10.1.1.1
```

Class A default:

```text
/8
```

Example:

```text
172.16.1.1
```

Class B default:

```text
/16
```

Example:

```text
192.168.1.1
```

Class C default:

```text
/24
```

But if the question gives a prefix, use the prefix.

Example:

```text
172.16.1.1/24
```

Real prefix:

```text
/24
```

Not classful `/16`.

---

## 21. Quick method: prefix to host count

For a host requirement question:

```text
How many hosts are needed?
```

Use this thinking:

```text
Need 100 hosts -> /25 gives 126 usable -> OK
Need 60 hosts  -> /26 gives 62 usable  -> OK
Need 30 hosts  -> /27 gives 30 usable  -> OK
Need 14 hosts  -> /28 gives 14 usable  -> OK
Need 6 hosts   -> /29 gives 6 usable   -> OK
Need 2 hosts   -> /30 gives 2 usable   -> OK
```

You choose the smallest subnet that supports the required number of hosts.

This is important for VLSM later.

---

## 22. Common CCNA question style

Question:

```text
Which subnet mask supports at least 50 hosts?
```

Check:

```text
/27 = 30 usable hosts -> too small
/26 = 62 usable hosts -> enough
```

Answer:

```text
/26
255.255.255.192
```

---

Question:

```text
Which subnet mask supports at least 200 hosts?
```

Check:

```text
/25 = 126 usable hosts -> too small
/24 = 254 usable hosts -> enough
```

Answer:

```text
/24
255.255.255.0
```

---

Question:

```text
Which subnet mask supports at least 500 hosts?
```

Check:

```text
/24 = 254 usable hosts -> too small
/23 = 510 usable hosts -> enough
```

Answer:

```text
/23
255.255.254.0
```

---

## 23. Common mistake: confusing number of addresses with usable hosts

Example:

```text
/26
```

Total addresses:

```text
64
```

Usable hosts:

```text
62
```

Why?

```text
1 network address
1 broadcast address
```

So:

```text
64 - 2 = 62
```

Do not answer 64 if the question asks for usable hosts.

---

## 24. Common mistake: thinking /24 is always Class C only

`/24` can be used with any IP range.

Examples:

```text
10.10.10.0/24
172.16.50.0/24
192.168.1.0/24
```

All of these are valid `/24` networks.

The prefix defines the subnet size, not the old class.

---

## 25. Common mistake: invalid mask values

Valid mask octet values are:

```text
0
128
192
224
240
248
252
254
255
```

So this mask is invalid:

```text
255.255.255.250
```

Because `250` is not a valid subnet mask octet.

This mask is invalid:

```text
255.255.10.0
```

Because `10` is not a valid subnet mask octet.

This mask is invalid:

```text
255.0.255.0
```

Because 1s and 0s would not be continuous.

---

## 26. What to remember after Lesson 3

Key points:

```text
CIDR is the slash notation, such as /24 or /26.
The prefix tells how many bits are network bits.
IPv4 has 32 bits.
Host bits = 32 - prefix.
Subnet mask uses 1s for network bits and 0s for host bits.
CIDR and subnet mask describe the same thing.
Usable hosts = 2^host_bits - 2.
Valid subnet masks have continuous 1s followed by continuous 0s.
The old class does not override the written prefix.
Always use the prefix or subnet mask given in the question.
```

---

## 27. Mini homework

### Part A — Prefix to mask

Write the subnet mask:

```text
1. /24
2. /25
3. /26
4. /27
5. /28
6. /29
7. /30
8. /20
9. /22
10. /23
```

### Part B — Prefix to host bits and usable hosts

Calculate host bits and usable hosts:

```text
1. /24
2. /25
3. /26
4. /27
5. /28
6. /29
7. /30
8. /23
9. /22
10. /20
```

### Part C — Valid or invalid mask?

Decide whether the mask is valid:

```text
1. 255.255.255.0
2. 255.255.255.192
3. 255.255.255.250
4. 255.255.0.0
5. 255.0.255.0
6. 255.255.240.0
7. 255.255.224.0
8. 255.255.255.127
9. 255.255.252.0
10. 255.255.10.0
```

---

## 28. Answers

### Part A

```text
1. /24 = 255.255.255.0
2. /25 = 255.255.255.128
3. /26 = 255.255.255.192
4. /27 = 255.255.255.224
5. /28 = 255.255.255.240
6. /29 = 255.255.255.248
7. /30 = 255.255.255.252
8. /20 = 255.255.240.0
9. /22 = 255.255.252.0
10. /23 = 255.255.254.0
```

### Part B

```text
1. /24: 8 host bits, 254 usable hosts
2. /25: 7 host bits, 126 usable hosts
3. /26: 6 host bits, 62 usable hosts
4. /27: 5 host bits, 30 usable hosts
5. /28: 4 host bits, 14 usable hosts
6. /29: 3 host bits, 6 usable hosts
7. /30: 2 host bits, 2 usable hosts
8. /23: 9 host bits, 510 usable hosts
9. /22: 10 host bits, 1022 usable hosts
10. /20: 12 host bits, 4094 usable hosts
```

### Part C

```text
1. 255.255.255.0     = valid
2. 255.255.255.192   = valid
3. 255.255.255.250   = invalid
4. 255.255.0.0       = valid
5. 255.0.255.0       = invalid
6. 255.255.240.0     = valid
7. 255.255.224.0     = valid
8. 255.255.255.127   = invalid
9. 255.255.252.0     = valid
10. 255.255.10.0     = invalid
```
