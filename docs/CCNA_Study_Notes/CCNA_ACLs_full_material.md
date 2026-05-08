# CCNA Study Material: Access Control Lists (ACLs)

## 1. What is an ACL?

An **Access Control List**, usually called an **ACL**, is a set of rules used by a router or Layer 3 switch to control network traffic.

An ACL can:

- permit traffic,
- deny traffic,
- filter traffic by source IP address,
- filter traffic by destination IP address,
- filter traffic by protocol, such as TCP, UDP, or ICMP,
- filter traffic by port number, such as HTTP, HTTPS, DNS, SSH,
- control access to router management lines,
- help protect networks by allowing only required traffic.

In simple words:

> An ACL is a traffic filter.  
> It checks packets against rules and decides whether the packet is allowed or blocked.

---

## 2. Why do we use ACLs?

ACLs are used for several important reasons.

### 2.1 Security

Example:

You may want to block users from the Guest network from accessing the Server network.

### 2.2 Traffic control

Example:

You may want to allow only DNS traffic to a DNS server and block everything else.

### 2.3 Network segmentation

Example:

VLAN 10 can access the internet, but it cannot access VLAN 20.

### 2.4 Router management protection

Example:

Only your admin PC should be allowed to connect to a router using SSH.

### 2.5 NAT traffic selection

ACLs can identify which private IP addresses are allowed to be translated by NAT.

---

## 3. How an ACL works

An ACL contains multiple lines. Each line is called an **Access Control Entry**, or **ACE**.

The router checks packets:

1. from the top of the ACL,
2. line by line,
3. until it finds the first matching rule,
4. then it applies that rule,
5. and stops checking the rest of the ACL.

This is very important:

> ACLs are processed from top to bottom.  
> The first match wins.

Example:

```text
access-list 10 deny 192.168.1.10
access-list 10 permit 192.168.1.0 0.0.0.255
```

If the packet comes from `192.168.1.10`, it matches the first line and is denied.

The second line would normally permit the whole `192.168.1.0/24` network, but it does not matter for `192.168.1.10`, because the first match already denied it.

---

## 4. Implicit deny

Every ACL has an invisible line at the end:

```text
deny any
```

This line is not shown in the configuration, but it is always there.

This means:

> If traffic does not match any permit rule, it will be denied automatically.

Example:

```text
access-list 10 permit 192.168.1.0 0.0.0.255
```

This ACL allows traffic from `192.168.1.0/24`.

Everything else is denied because of the hidden implicit deny.

The real logic is:

```text
access-list 10 permit 192.168.1.0 0.0.0.255
access-list 10 deny any
```

But the second line is hidden.

---

## 5. Types of ACLs in CCNA

For CCNA, the most important ACL types are:

1. Standard ACLs
2. Extended ACLs
3. Named ACLs
4. Numbered ACLs
5. ACLs for VTY lines

---

# Part 1: Standard ACLs

## 6. Standard ACLs

A **standard ACL** filters traffic only by **source IPv4 address**.

It does not check:

- destination IP,
- protocol,
- TCP/UDP port,
- application.

It only asks:

> Where is this traffic coming from?

---

## 7. Standard ACL number ranges

Traditional standard ACL numbers are:

```text
1-99
1300-1999
```

Example:

```text
access-list 10 permit 192.168.1.0 0.0.0.255
```

Here, ACL number `10` is a standard ACL because it is in the range `1-99`.

---

## 8. Standard ACL syntax

Basic syntax:

```text
access-list access-list-number permit|deny source wildcard-mask
```

Example:

```text
access-list 10 permit 192.168.1.0 0.0.0.255
```

Meaning:

```text
Access list 10 permits traffic from network 192.168.1.0/24
```

---

## 9. Example from the screenshot

The screenshot shows this ACL:

```text
access-list 10 permit 192.168.1.0 0.0.0.255
access-list 10 deny any
interface GigabitEthernet0/0
 ip access-group 10 in
```

Explanation:

```text
access-list 10 permit 192.168.1.0 0.0.0.255
```

This allows all source IP addresses from:

```text
192.168.1.0 to 192.168.1.255
```

because the wildcard mask `0.0.0.255` means:

- first 3 octets must match exactly,
- last octet can be anything.

```text
access-list 10 deny any
```

This denies all other source IP addresses.

This line is technically not required because ACLs already have an implicit deny at the end, but it is sometimes written manually to make the logic visible.

```text
interface GigabitEthernet0/0
```

This enters interface configuration mode.

```text
ip access-group 10 in
```

This applies ACL 10 to inbound traffic on interface `GigabitEthernet0/0`.

So the full meaning is:

> On interface GigabitEthernet0/0, allow inbound packets only if their source IP address belongs to 192.168.1.0/24. Deny everything else.

---

## 10. Wildcard masks

Cisco ACLs use **wildcard masks**, not subnet masks.

A wildcard mask tells the router which bits must match and which bits can be ignored.

The rule is:

```text
0 = must match
1 = ignore
```

This is the opposite of a subnet mask.

### 10.1 Common wildcard masks

| Subnet | Subnet Mask | Wildcard Mask |
|---|---:|---:|
| /32 | 255.255.255.255 | 0.0.0.0 |
| /30 | 255.255.255.252 | 0.0.0.3 |
| /29 | 255.255.255.248 | 0.0.0.7 |
| /28 | 255.255.255.240 | 0.0.0.15 |
| /27 | 255.255.255.224 | 0.0.0.31 |
| /26 | 255.255.255.192 | 0.0.0.63 |
| /25 | 255.255.255.128 | 0.0.0.127 |
| /24 | 255.255.255.0 | 0.0.0.255 |
| /23 | 255.255.254.0 | 0.0.1.255 |
| /22 | 255.255.252.0 | 0.0.3.255 |
| /21 | 255.255.248.0 | 0.0.7.255 |
| /20 | 255.255.240.0 | 0.0.15.255 |
| /16 | 255.255.0.0 | 0.0.255.255 |
| /8 | 255.0.0.0 | 0.255.255.255 |

---

## 11. How to calculate a wildcard mask

Formula:

```text
Wildcard mask = 255.255.255.255 - subnet mask
```

Example for `/24`:

```text
Subnet mask:   255.255.255.0
Subtract from: 255.255.255.255
Wildcard:        0.  0.  0.255
```

So:

```text
192.168.1.0/24 = 192.168.1.0 0.0.0.255
```

Example for `/26`:

```text
Subnet mask:   255.255.255.192
Subtract from: 255.255.255.255
Wildcard:        0.  0.  0. 63
```

So:

```text
192.168.1.0/26 = 192.168.1.0 0.0.0.63
```

---

## 12. The keyword `host`

Instead of writing:

```text
access-list 10 permit 192.168.1.10 0.0.0.0
```

you can write:

```text
access-list 10 permit host 192.168.1.10
```

Both mean:

> Match exactly one IP address: 192.168.1.10.

---

## 13. The keyword `any`

Instead of writing:

```text
access-list 10 permit 0.0.0.0 255.255.255.255
```

you can write:

```text
access-list 10 permit any
```

Both mean:

> Match any source IP address.

---

## 14. Standard ACL example: allow one network

Goal:

Allow only network `192.168.10.0/24`.

Configuration:

```text
R1(config)# access-list 10 permit 192.168.10.0 0.0.0.255
R1(config)# interface g0/0
R1(config-if)# ip access-group 10 in
```

Because of implicit deny, all other networks are blocked.

---

## 15. Standard ACL example: block one host, allow everyone else

Goal:

Block host `192.168.10.50`, but allow all other traffic.

Configuration:

```text
R1(config)# access-list 10 deny host 192.168.10.50
R1(config)# access-list 10 permit any
R1(config)# interface g0/0
R1(config-if)# ip access-group 10 in
```

Important:

The `permit any` line is required. Without it, everything else would be blocked by implicit deny.

---

## 16. Standard ACL placement rule

Standard ACLs should normally be placed:

> as close to the destination as possible.

Why?

Because standard ACLs only check the source address. If you place them too close to the source, you may block that source from reaching many destinations, even if you only wanted to block one specific destination.

Example:

PC1 should not access Server1, but PC1 should still access the internet.

If you put a standard ACL near PC1, you may block PC1 from everything.

---

# Part 2: Extended ACLs

## 17. Extended ACLs

An **extended ACL** can filter traffic by:

- source IP,
- destination IP,
- protocol,
- TCP port,
- UDP port,
- ICMP type.

Extended ACLs are much more precise than standard ACLs.

A standard ACL asks:

> Where is the traffic coming from?

An extended ACL asks:

> Where is the traffic coming from, where is it going, and what type of traffic is it?

---

## 18. Extended ACL number ranges

Traditional extended ACL numbers are:

```text
100-199
2000-2699
```

Example:

```text
access-list 100 deny tcp 192.168.10.0 0.0.0.255 host 192.168.20.10 eq 80
```

ACL number `100` is an extended ACL.

---

## 19. Extended ACL syntax

Basic syntax:

```text
access-list number permit|deny protocol source source-wildcard destination destination-wildcard operator port
```

Example:

```text
access-list 100 permit tcp 192.168.10.0 0.0.0.255 host 192.168.20.10 eq 80
```

Meaning:

> Permit TCP traffic from 192.168.10.0/24 to host 192.168.20.10, but only if the destination port is 80.

Port 80 = HTTP.

---

## 20. Common protocols

| Protocol | Meaning |
|---|---|
| ip | All IPv4 traffic |
| tcp | TCP traffic |
| udp | UDP traffic |
| icmp | Ping and ICMP messages |
| ospf | OSPF routing protocol |
| eigrp | EIGRP routing protocol |
| gre | GRE tunnel traffic |

Important:

```text
ip
```

means all IPv4 protocols.

Example:

```text
access-list 100 deny ip any any
```

means deny all IPv4 traffic.

---

## 21. Common TCP and UDP ports

| Service | Protocol | Port |
|---|---|---:|
| FTP Data | TCP | 20 |
| FTP Control | TCP | 21 |
| SSH | TCP | 22 |
| Telnet | TCP | 23 |
| SMTP | TCP | 25 |
| DNS | TCP/UDP | 53 |
| DHCP Server | UDP | 67 |
| DHCP Client | UDP | 68 |
| HTTP | TCP | 80 |
| POP3 | TCP | 110 |
| NTP | UDP | 123 |
| IMAP | TCP | 143 |
| HTTPS | TCP | 443 |
| SMB | TCP | 445 |
| RDP | TCP | 3389 |

---

## 22. Port operators

| Operator | Meaning | Example |
|---|---|---|
| eq | equal to | eq 80 |
| neq | not equal to | neq 80 |
| gt | greater than | gt 1023 |
| lt | less than | lt 1024 |
| range | range of ports | range 20 21 |

Example:

```text
access-list 100 permit tcp any any eq 443
```

Meaning:

> Permit TCP traffic to destination port 443.

---

## 23. Extended ACL example: block HTTP to one server

Goal:

Block VLAN 10 from accessing web server `192.168.20.10` using HTTP, but allow all other traffic.

Configuration:

```text
R1(config)# access-list 100 deny tcp 192.168.10.0 0.0.0.255 host 192.168.20.10 eq 80
R1(config)# access-list 100 permit ip any any
R1(config)# interface g0/0
R1(config-if)# ip access-group 100 in
```

Explanation:

```text
deny tcp 192.168.10.0 0.0.0.255 host 192.168.20.10 eq 80
```

Deny HTTP traffic from VLAN 10 to the server.

```text
permit ip any any
```

Allow everything else.

Without `permit ip any any`, all other traffic would be denied by implicit deny.

---

## 24. Extended ACL example: allow only DNS to DNS server

Goal:

Allow clients in `192.168.10.0/24` to query DNS server `192.168.20.53`.

Configuration:

```text
R1(config)# access-list 101 permit udp 192.168.10.0 0.0.0.255 host 192.168.20.53 eq 53
R1(config)# access-list 101 permit tcp 192.168.10.0 0.0.0.255 host 192.168.20.53 eq 53
R1(config)# access-list 101 deny ip 192.168.10.0 0.0.0.255 host 192.168.20.53
R1(config)# access-list 101 permit ip any any
R1(config)# interface g0/0
R1(config-if)# ip access-group 101 in
```

Why both UDP and TCP?

DNS normally uses UDP port 53, but TCP port 53 can also be used, especially for larger responses or zone transfers.

---

## 25. Extended ACL example: allow SSH only from admin PC

Goal:

Only admin PC `192.168.10.50` can SSH to server `192.168.20.10`.

Configuration:

```text
R1(config)# access-list 102 permit tcp host 192.168.10.50 host 192.168.20.10 eq 22
R1(config)# access-list 102 deny tcp any host 192.168.20.10 eq 22
R1(config)# access-list 102 permit ip any any
R1(config)# interface g0/0
R1(config-if)# ip access-group 102 in
```

Explanation:

Line 1 permits SSH from the admin PC.

Line 2 blocks SSH from everyone else.

Line 3 allows all other traffic.

---

## 26. Extended ACL placement rule

Extended ACLs should normally be placed:

> as close to the source as possible.

Why?

Because extended ACLs are specific. They can check source, destination, and port.

If traffic should be blocked, it is better to block it early before it travels through the network.

---

# Part 3: Applying ACLs to interfaces

## 27. Inbound vs outbound ACLs

An ACL can be applied in two directions:

```text
in
out
```

### 27.1 Inbound ACL

An inbound ACL checks traffic as it enters an interface.

Example:

```text
interface g0/0
 ip access-group 100 in
```

Meaning:

> Check packets entering interface g0/0.

### 27.2 Outbound ACL

An outbound ACL checks traffic as it leaves an interface.

Example:

```text
interface g0/1
 ip access-group 100 out
```

Meaning:

> Check packets leaving interface g0/1.

---

## 28. Important interface rule

For each interface, each protocol, and each direction, you can apply only one ACL.

For IPv4:

- one inbound IPv4 ACL,
- one outbound IPv4 ACL.

Example:

```text
interface g0/0
 ip access-group 100 in
 ip access-group 101 out
```

This is valid because one ACL is inbound and one ACL is outbound.

But this is not valid as two inbound IPv4 ACLs:

```text
interface g0/0
 ip access-group 100 in
 ip access-group 101 in
```

The second command would replace the first one.

---

## 29. Removing ACLs from interfaces

To remove an ACL from an interface:

```text
R1(config)# interface g0/0
R1(config-if)# no ip access-group 100 in
```

This does not delete the ACL itself. It only removes it from the interface.

To delete the ACL completely:

```text
R1(config)# no access-list 100
```

---

# Part 4: Named ACLs

## 30. Named ACLs

A named ACL uses a name instead of only a number.

Named ACLs are easier to understand and manage.

Example:

```text
ip access-list extended BLOCK_GUEST_TO_SERVER
 deny tcp 192.168.30.0 0.0.0.255 host 192.168.20.10 eq 22
 permit ip any any
```

This is easier to understand than:

```text
access-list 100 deny tcp 192.168.30.0 0.0.0.255 host 192.168.20.10 eq 22
access-list 100 permit ip any any
```

---

## 31. Named standard ACL syntax

```text
R1(config)# ip access-list standard ACL_NAME
R1(config-std-nacl)# permit 192.168.1.0 0.0.0.255
R1(config-std-nacl)# deny any
```

Example:

```text
R1(config)# ip access-list standard LAN_ONLY
R1(config-std-nacl)# permit 192.168.1.0 0.0.0.255
R1(config-std-nacl)# deny any
R1(config)# interface g0/0
R1(config-if)# ip access-group LAN_ONLY in
```

---

## 32. Named extended ACL syntax

```text
R1(config)# ip access-list extended ACL_NAME
R1(config-ext-nacl)# permit|deny protocol source source-wildcard destination destination-wildcard operator port
```

Example:

```text
R1(config)# ip access-list extended WEB_ONLY
R1(config-ext-nacl)# permit tcp 192.168.10.0 0.0.0.255 host 192.168.20.10 eq 80
R1(config-ext-nacl)# permit tcp 192.168.10.0 0.0.0.255 host 192.168.20.10 eq 443
R1(config-ext-nacl)# deny ip any host 192.168.20.10
R1(config-ext-nacl)# permit ip any any
```

---

## 33. Sequence numbers

Named ACLs use sequence numbers.

Example:

```text
10 permit tcp 192.168.10.0 0.0.0.255 host 192.168.20.10 eq 80
20 permit tcp 192.168.10.0 0.0.0.255 host 192.168.20.10 eq 443
30 deny ip any host 192.168.20.10
40 permit ip any any
```

Sequence numbers allow you to insert or remove specific ACL lines.

Example:

```text
R1(config)# ip access-list extended WEB_ONLY
R1(config-ext-nacl)# 15 permit icmp any host 192.168.20.10
```

This inserts a new rule between sequence 10 and 20.

---

# Part 5: ACLs for VTY lines

## 34. Controlling SSH or Telnet access to a router

ACLs can also protect router management access.

Example goal:

Only admin PC `192.168.10.50` should be allowed to SSH to the router.

Configuration:

```text
R1(config)# access-list 5 permit host 192.168.10.50
R1(config)# line vty 0 4
R1(config-line)# access-class 5 in
```

Important:

For VTY lines, use:

```text
access-class
```

not:

```text
ip access-group
```

`ip access-group` is used on interfaces.

`access-class` is used on VTY lines.

---

# Part 6: Verification and troubleshooting

## 35. Useful show commands

### Show all ACLs

```text
show access-lists
```

This shows all configured ACLs and packet match counters.

### Show one ACL

```text
show access-lists 100
```

### Show running configuration

```text
show running-config
```

or shorter:

```text
show run
```

### Show interface configuration

```text
show running-config interface g0/0
```

### Show IP interface details

```text
show ip interface g0/0
```

This can show whether an ACL is applied inbound or outbound.

---

## 36. Packet counters

When you run:

```text
show access-lists
```

you may see something like:

```text
10 permit tcp 192.168.10.0 0.0.0.255 host 192.168.20.10 eq 80 (25 matches)
20 deny ip any any (4 matches)
```

The matches tell you how many packets matched each ACL line.

This is useful for troubleshooting.

If an ACL line has zero matches, possible reasons include:

- traffic is not reaching that interface,
- ACL is applied in the wrong direction,
- wildcard mask is wrong,
- source or destination IP is wrong,
- another line above it already matches the traffic,
- the traffic uses a different protocol or port.

---

## 37. Common ACL mistakes

### Mistake 1: Forgetting implicit deny

Example:

```text
access-list 100 deny tcp any host 192.168.20.10 eq 80
```

This blocks HTTP to the server, but it also blocks everything else because there is no permit rule after it.

Correct version:

```text
access-list 100 deny tcp any host 192.168.20.10 eq 80
access-list 100 permit ip any any
```

### Mistake 2: Wrong ACL direction

You may apply an ACL inbound when traffic is actually leaving the interface, or outbound when traffic is entering the interface.

Always ask:

> Where does the packet enter the router?  
> Where does the packet leave the router?

### Mistake 3: Wrong wildcard mask

Example:

```text
access-list 10 permit 192.168.1.0 255.255.255.0
```

This is wrong because `255.255.255.0` is a subnet mask, not a wildcard mask.

Correct:

```text
access-list 10 permit 192.168.1.0 0.0.0.255
```

### Mistake 4: Wrong order

Wrong:

```text
access-list 100 permit ip any any
access-list 100 deny tcp any host 192.168.20.10 eq 80
```

The deny line will never work because `permit ip any any` matches first.

Correct:

```text
access-list 100 deny tcp any host 192.168.20.10 eq 80
access-list 100 permit ip any any
```

### Mistake 5: Applying a standard ACL too close to the source

Standard ACLs only check source IP, so placing them too early can block more than intended.

---

# Part 7: Practical CCNA lab

## 38. Lab topology

Imagine this topology:

```text
PC1 ---- VLAN 10 ---- R1 ---- VLAN 20 ---- Server1
```

Addresses:

```text
PC1:      192.168.10.10/24
Gateway:  192.168.10.1

Server1:  192.168.20.10/24
Gateway:  192.168.20.1
```

Goal:

1. PC1 should not access Server1 using HTTP.
2. PC1 should still be able to ping Server1.
3. PC1 should still access other networks.

Correct ACL:

```text
R1(config)# access-list 100 deny tcp host 192.168.10.10 host 192.168.20.10 eq 80
R1(config)# access-list 100 permit ip any any
R1(config)# interface g0/0
R1(config-if)# ip access-group 100 in
```

Explanation:

- `deny tcp` blocks only TCP.
- `eq 80` blocks only HTTP.
- ICMP is still allowed because of `permit ip any any`.
- Other traffic is still allowed.

---

## 39. Test commands

From PC1:

```text
ping 192.168.20.10
```

This should work.

Try HTTP:

```text
http://192.168.20.10
```

This should fail.

On the router:

```text
show access-lists
```

You should see matches on the deny line when HTTP traffic is attempted.

---

# Part 8: ACL decision process

When solving ACL questions in CCNA, use this process.

## Step 1: Identify what must be blocked or allowed

Ask:

- What source?
- What destination?
- What protocol?
- What port?

## Step 2: Choose standard or extended ACL

Use standard ACL when filtering only by source IP.

Use extended ACL when filtering by:

- destination,
- protocol,
- port.

## Step 3: Decide where to place it

Standard ACL:

```text
close to destination
```

Extended ACL:

```text
close to source
```

## Step 4: Decide direction

Ask:

- Is traffic entering this interface?
- Is traffic leaving this interface?

Then choose:

```text
in
```

or:

```text
out
```

## Step 5: Add required permit statements

Remember:

> Everything not permitted is denied.

## Step 6: Verify

Use:

```text
show access-lists
show ip interface
show running-config
```

---

# Part 9: Mini cheat sheet

## Standard ACL

```text
access-list 10 permit 192.168.1.0 0.0.0.255
interface g0/0
 ip access-group 10 in
```

Filters by:

```text
source IP only
```

Placement:

```text
close to destination
```

---

## Extended ACL

```text
access-list 100 deny tcp 192.168.10.0 0.0.0.255 host 192.168.20.10 eq 80
access-list 100 permit ip any any
interface g0/0
 ip access-group 100 in
```

Filters by:

```text
source, destination, protocol, port
```

Placement:

```text
close to source
```

---

## Named extended ACL

```text
ip access-list extended BLOCK_HTTP
 deny tcp 192.168.10.0 0.0.0.255 host 192.168.20.10 eq 80
 permit ip any any
interface g0/0
 ip access-group BLOCK_HTTP in
```

---

## VTY ACL

```text
access-list 5 permit host 192.168.10.50
line vty 0 4
 access-class 5 in
```

---

# Part 10: Practice questions

## Question 1

You want to allow only network `192.168.1.0/24` through an interface. Which ACL line is correct?

Answer:

```text
access-list 10 permit 192.168.1.0 0.0.0.255
```

---

## Question 2

What happens if no ACL line matches the packet?

Answer:

The packet is denied by the implicit deny at the end of the ACL.

---

## Question 3

Which ACL type can filter by destination port?

Answer:

Extended ACL.

---

## Question 4

Where should a standard ACL usually be placed?

Answer:

Close to the destination.

---

## Question 5

Where should an extended ACL usually be placed?

Answer:

Close to the source.

---

## Question 6

What does this line mean?

```text
access-list 100 permit tcp any host 192.168.20.10 eq 443
```

Answer:

Permit TCP traffic from any source to destination host `192.168.20.10` if the destination port is `443`, which is HTTPS.

---

## Question 7

What is wrong with this ACL?

```text
access-list 100 permit ip any any
access-list 100 deny tcp any host 192.168.20.10 eq 80
```

Answer:

The deny line will never work because `permit ip any any` is above it. ACLs are checked from top to bottom, and the first match wins.

---

# Part 11: Summary

ACLs are one of the most important CCNA topics.

The key points are:

1. ACLs filter traffic.
2. ACLs are checked from top to bottom.
3. The first matching rule wins.
4. Every ACL has an implicit `deny any` at the end.
5. Standard ACLs filter by source IP only.
6. Extended ACLs filter by source, destination, protocol, and port.
7. Standard ACLs are usually placed close to the destination.
8. Extended ACLs are usually placed close to the source.
9. ACLs can be applied inbound or outbound.
10. Always verify using `show access-lists`, `show ip interface`, and `show running-config`.

---

# Part 12: Commands to remember

```text
show access-lists
show access-lists 100
show running-config
show running-config interface g0/0
show ip interface g0/0
```

```text
no ip access-group 100 in
no access-list 100
```

```text
ip access-list extended NAME
ip access-list standard NAME
```

---

End of material.
