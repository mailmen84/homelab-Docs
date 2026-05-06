# CCNA Subnetting – Lesson 2
# Classful IPv4 Addressing and Private/Public IPv4 Ranges

## Goal of this lesson

After this lesson you should understand:

```text
1. What classful IPv4 addressing means.
2. What Class A, Class B, Class C, Class D, and Class E are.
3. How to recognise the class of an IPv4 address.
4. What default subnet masks are.
5. Why classful addressing is old but still important for CCNA.
6. What private IPv4 ranges are.
7. What public IPv4 addresses are.
8. Why private IP addresses need NAT to reach the Internet.
9. What special IPv4 ranges you should recognise.
```

---

## 1. What does “classful addressing” mean?

In the early design of IPv4, addresses were divided into fixed classes.

These classes were:

```text
Class A
Class B
Class C
Class D
Class E
```

This system is called:

```text
classful addressing
```

In classful addressing, the first octet decides the address class.

Example:

```text
10.1.1.1       -> Class A
172.16.1.1     -> Class B
192.168.1.1    -> Class C
224.1.1.1      -> Class D
240.1.1.1      -> Class E
```

Modern networks use CIDR, not strict classful addressing. But CCNA still expects you to understand classes because default masks and many older subnetting concepts come from this system.

---

## 2. Why did classes exist?

IPv4 addresses were divided into classes because different organisations needed networks of different sizes.

```text
Class A = very large networks
Class B = medium networks
Class C = small networks
Class D = multicast
Class E = experimental/reserved
```

---

## 3. IPv4 class ranges

The class is determined by the first octet.

```text
Class A: 1 - 126
Class B: 128 - 191
Class C: 192 - 223
Class D: 224 - 239
Class E: 240 - 255
```

Important: `127.x.x.x` is reserved for loopback, so the normal Class A usable range is described as `1-126`.

---

## 4. Class A

Class A range:

```text
1.0.0.0 - 126.255.255.255
```

Default subnet mask:

```text
255.0.0.0
```

CIDR notation:

```text
/8
```

Class A default structure:

```text
Network.Host.Host.Host
```

Example:

```text
10.1.2.3
```

Using default Class A mask `/8`:

```text
Network portion = 10
Host portion    = 1.2.3
```

Class A gives a small number of very large networks.

---

## 5. Class A host count

Class A default prefix:

```text
/8
```

Host bits:

```text
32 - 8 = 24
```

Usable hosts:

```text
2^24 - 2 = 16,777,214
```

One default Class A network can contain over 16 million usable host addresses.

---

## 6. Class B

Class B range:

```text
128.0.0.0 - 191.255.255.255
```

Default subnet mask:

```text
255.255.0.0
```

CIDR notation:

```text
/16
```

Class B default structure:

```text
Network.Network.Host.Host
```

Example:

```text
172.16.5.10
```

Using default Class B mask `/16`:

```text
Network portion = 172.16
Host portion    = 5.10
```

---

## 7. Class B host count

Class B default prefix:

```text
/16
```

Host bits:

```text
32 - 16 = 16
```

Usable hosts:

```text
2^16 - 2 = 65,534
```

One default Class B network can contain 65,534 usable host addresses.

---

## 8. Class C

Class C range:

```text
192.0.0.0 - 223.255.255.255
```

Default subnet mask:

```text
255.255.255.0
```

CIDR notation:

```text
/24
```

Class C default structure:

```text
Network.Network.Network.Host
```

Example:

```text
192.168.1.10
```

Using default Class C mask `/24`:

```text
Network portion = 192.168.1
Host portion    = 10
```

---

## 9. Class C host count

Class C default prefix:

```text
/24
```

Host bits:

```text
32 - 24 = 8
```

Usable hosts:

```text
2^8 - 2 = 254
```

One default Class C network can contain 254 usable host addresses.

---

## 10. Class D

Class D range:

```text
224.0.0.0 - 239.255.255.255
```

Class D is used for multicast.

Multicast means one sender can send traffic to a group of receivers.

Class D is not used for normal host addressing.

Examples:

```text
224.0.0.5  = OSPF routers
224.0.0.10 = EIGRP routers
```

For CCNA:

```text
Class D = multicast
Range: 224 - 239
```

---

## 11. Class E

Class E range:

```text
240.0.0.0 - 255.255.255.255
```

Class E is reserved/experimental. It is not used for normal host addressing.

For CCNA:

```text
Class E = reserved/experimental
Range: 240 - 255
```

---

## 12. Classful addressing summary table

| Class | First octet range | Default mask | CIDR | Main use |
|---|---:|---|---:|---|
| A | 1–126 | 255.0.0.0 | /8 | Very large networks |
| B | 128–191 | 255.255.0.0 | /16 | Medium networks |
| C | 192–223 | 255.255.255.0 | /24 | Small networks |
| D | 224–239 | Not normal host mask | N/A | Multicast |
| E | 240–255 | Not normal host mask | N/A | Reserved/experimental |

---

## 13. Why is 127 missing?

The range:

```text
127.0.0.0/8
```

is reserved for loopback.

The most famous loopback address is:

```text
127.0.0.1
```

This means:

```text
this device itself
```

If you ping `127.0.0.1`, you are testing the local TCP/IP stack on your own machine.

---

## 14. Why classful addressing is old

Classful addressing was inefficient.

Example: a company needs 500 addresses.

```text
Class C = 254 usable hosts -> too small
Class B = 65,534 usable hosts -> far too large
```

CIDR solved this by allowing flexible prefix lengths:

```text
/23 = 510 usable hosts
/22 = 1022 usable hosts
/21 = 2046 usable hosts
```

Instead of being forced into Class A, B, or C default sizes, modern networks can use more precise subnet sizes.

---

## 15. Classful vs CIDR

Classful thinking:

```text
192.168.1.1 is Class C, so default mask is /24.
```

CIDR thinking:

```text
192.168.1.1/27 has a /27 mask, regardless of being Class C.
```

Today, the prefix tells you the real network size.

Example:

```text
192.168.1.1/27
```

Even though `192` is Class C, the actual mask is:

```text
/27 = 255.255.255.224
```

For CCNA, always pay attention to the prefix or subnet mask given in the question.

---

## 16. Private IPv4 addresses

Some IPv4 ranges are reserved for private networks.

Private IP addresses are used inside homes, companies, labs, factories, schools, and data centres.

They are not routed on the public Internet.

The private IPv4 ranges are:

```text
10.0.0.0/8
172.16.0.0/12
192.168.0.0/16
```

You must memorise these.

---

## 17. Private range 1: 10.0.0.0/8

Range:

```text
10.0.0.0 - 10.255.255.255
```

CIDR:

```text
10.0.0.0/8
```

This is a huge private range. It is common in companies and enterprise networks.

Examples:

```text
10.1.1.1
10.10.20.5
10.100.50.25
10.255.255.254
```

---

## 18. Private range 2: 172.16.0.0/12

Range:

```text
172.16.0.0 - 172.31.255.255
```

CIDR:

```text
172.16.0.0/12
```

This is often misunderstood.

Not every `172.x.x.x` address is private.

Private is only:

```text
172.16.x.x to 172.31.x.x
```

Examples of private:

```text
172.16.1.1
172.20.10.5
172.31.255.254
```

Examples of public:

```text
172.15.1.1
172.32.1.1
```

This is a common exam trap.

---

## 19. Private range 3: 192.168.0.0/16

Range:

```text
192.168.0.0 - 192.168.255.255
```

CIDR:

```text
192.168.0.0/16
```

Common in home and small office networks.

Examples:

```text
192.168.0.1
192.168.1.1
192.168.20.10
192.168.255.254
```

Important: not all `192.x.x.x` addresses are private.

Only:

```text
192.168.x.x
```

is private.

---

## 20. Private IPv4 summary table

| Private range | CIDR | Address range |
|---|---:|---|
| 10.0.0.0 | /8 | 10.0.0.0 – 10.255.255.255 |
| 172.16.0.0 | /12 | 172.16.0.0 – 172.31.255.255 |
| 192.168.0.0 | /16 | 192.168.0.0 – 192.168.255.255 |

Memorise this table.

---

## 21. Public IPv4 addresses

A public IPv4 address is routable on the Internet.

Example public addresses:

```text
8.8.8.8
1.1.1.1
93.184.216.34
209.165.200.226
```

A private IPv4 address is not directly routable on the Internet.

Example private addresses:

```text
10.1.1.1
172.16.5.10
192.168.1.20
```

If a private device wants to access the Internet, it usually needs NAT.

---

## 22. NAT and private addresses

NAT means:

```text
Network Address Translation
```

NAT translates private IP addresses into a public IP address.

Example:

```text
PC private IP:       192.168.1.10
Router public IP:    203.0.113.50
Website:             8.8.8.8
```

When the PC goes to the Internet, the router changes the source IP from:

```text
192.168.1.10
```

to:

```text
203.0.113.50
```

The website sees the public IP, not the private IP.

---

## 23. APIPA address

APIPA range:

```text
169.254.0.0/16
```

If a device is configured for DHCP but cannot reach a DHCP server, it may assign itself an APIPA address.

Example:

```text
169.254.10.20
```

This usually means:

```text
The device did not receive a valid DHCP address.
```

For troubleshooting, if you see `169.254.x.x`, think DHCP problem.

---

## 24. Loopback address

Loopback range:

```text
127.0.0.0/8
```

Most common address:

```text
127.0.0.1
```

This points back to the local device and is used for testing.

---

## 25. Default route address

The address:

```text
0.0.0.0
```

has special meaning.

In routing, a default route is often written as:

```text
0.0.0.0/0
```

It means:

```text
any destination
```

Example:

```text
ip route 0.0.0.0 0.0.0.0 192.168.1.1
```

Meaning:

```text
Send traffic for unknown destinations to 192.168.1.1.
```

---

## 26. Broadcast address

The address:

```text
255.255.255.255
```

is the limited broadcast address.

It means:

```text
send to all hosts on the local network
```

It is not forwarded by routers.

---

## 27. Documentation/test networks

Some address ranges are reserved for documentation and examples.

Common examples:

```text
192.0.2.0/24
198.51.100.0/24
203.0.113.0/24
```

You may see them in books, documentation, and training material.

---

## 28. How to quickly identify address class

Look only at the first octet.

```text
10.1.1.1       -> 10 is between 1 and 126      -> Class A
172.16.1.1     -> 172 is between 128 and 191   -> Class B
192.168.1.1    -> 192 is between 192 and 223   -> Class C
224.0.0.5      -> 224 is between 224 and 239   -> Class D
240.1.1.1      -> 240 is between 240 and 255   -> Class E
```

---

## 29. Common exam traps

### Trap 1: Thinking all 172 addresses are private

Wrong:

```text
172.1.1.1 is private
```

Correct:

```text
Only 172.16.0.0 - 172.31.255.255 is private.
```

So:

```text
172.16.1.1 = private
172.31.1.1 = private
172.32.1.1 = public
172.15.1.1 = public
```

### Trap 2: Thinking all 192 addresses are private

Correct:

```text
Only 192.168.0.0/16 is private.
```

So:

```text
192.168.1.1 = private
192.167.1.1 = public
192.169.1.1 = public
```

### Trap 3: Ignoring the prefix

Question:

```text
192.168.1.100/27
```

Do not automatically use `/24` just because it starts with 192. The prefix is `/27`.

---

## 30. What to remember after Lesson 2

```text
Class A = 1-126, default /8
Class B = 128-191, default /16
Class C = 192-223, default /24
Class D = 224-239, multicast
Class E = 240-255, reserved/experimental

127.0.0.0/8 = loopback

Private IPv4:
10.0.0.0/8
172.16.0.0/12
192.168.0.0/16

169.254.0.0/16 = APIPA
0.0.0.0/0 = default route
255.255.255.255 = limited broadcast

Modern networking uses CIDR.
Always pay attention to the prefix/mask in the question.
```

---

## 31. Mini homework

### Part A — Identify the class

```text
1. 10.1.1.1
2. 126.5.5.5
3. 127.0.0.1
4. 128.1.1.1
5. 172.16.1.1
6. 191.255.1.1
7. 192.168.1.1
8. 223.1.1.1
9. 224.0.0.5
10. 240.1.1.1
```

### Part B — Private or public?

```text
1. 10.5.5.5
2. 11.5.5.5
3. 172.15.1.1
4. 172.16.1.1
5. 172.31.255.254
6. 172.32.1.1
7. 192.168.1.1
8. 192.169.1.1
9. 8.8.8.8
10. 1.1.1.1
```

### Part C — Default masks

```text
1. Class A
2. Class B
3. Class C
```

---

## 32. Answers

### Part A

```text
1. 10.1.1.1       = Class A
2. 126.5.5.5      = Class A
3. 127.0.0.1      = Loopback/reserved
4. 128.1.1.1      = Class B
5. 172.16.1.1     = Class B
6. 191.255.1.1    = Class B
7. 192.168.1.1    = Class C
8. 223.1.1.1      = Class C
9. 224.0.0.5      = Class D
10. 240.1.1.1     = Class E
```

### Part B

```text
1. 10.5.5.5        = Private
2. 11.5.5.5        = Public
3. 172.15.1.1      = Public
4. 172.16.1.1      = Private
5. 172.31.255.254  = Private
6. 172.32.1.1      = Public
7. 192.168.1.1     = Private
8. 192.169.1.1     = Public
9. 8.8.8.8         = Public
10. 1.1.1.1        = Public
```

### Part C

```text
1. Class A = 255.0.0.0 or /8
2. Class B = 255.255.0.0 or /16
3. Class C = 255.255.255.0 or /24
```
