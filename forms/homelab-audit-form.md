# Homelab Audit Form

## 1. Goal
- What should the homelab do: Main goal of my homelab is give me oportunity to learn how to manage servers and apps working on it. Im learning network administration using my switches. Other reason I have my homelab is run my services.
- What works well right now: In overall I think all running pretty well, my most consern is security. Some services could be set up better but for first time I cant complain. 
- What does not work / is annoying: At the moment most annoying is Openclaw service, witch is not running as I will expect. Other than tah I think I could do better work with my VLANs, and as I mention earlier security. 
- Priority areas (network / storage / AI / monitoring / other): I think we will have to check all areas one by one and make sure all I running as should. 

## 2. Topology
- Internet -> (what?) -> (what?) -> LAN:
- Do you have pfSense? (yes/no):yes
- Do you have an additional router? (yes/no):no 
- Do you use an ISP modem/bridge? (yes/no):yes
- Are VLANs used? (yes/no):yes
- Is there a network diagram? (yes/no):no

## 3. Network Devices

### Router / Firewall
- Model:Computer PC with 2 x NICs
- System (pfSense / OPNsense / other):pfsense
- IP:192.168.10.1
- Where it runs (bare metal / VM):Bare metal
- Interfaces:
- Problems / notes: Add more VLANs and check overall setings 

### Switches
Fill one section for each switch.

- Model:Buffalo BS-MP2012 
- Managed? (yes/no):yes
- IP:
- VLAN support:yes
- Uplink to:
- Problems / notes:

- Model:CISCO 
- Managed? (yes/no):yes
- IP:
- VLAN support:yes
- Uplink to:
- Problems / notes:

### Access Points
Fill one section for each AP.

- Model:unifi pro
- SSIDs:
- VLAN mapping:no
- Problems / notes:

## 4. VLANs and Addressing
- VLAN list:
  - VLAN ID:
  - Name:
  - Subnet:
  - Purpose:
- Inter-VLAN routing:
  - full / limited / none
- Problems / confusion:
VLAN 10 - Management / LAN
Service	IP Address	URL / Access	Status	Notes
Gateway / pfSense	192.168.10.1	https://192.168.10.1:8443	Needs confirmation	Gateway detected with DNS and nginx on port 8443
Pi-hole	192.168.10.2	http://192.168.10.2/admin	Needs confirmation	Raspberry Pi / Pi-hole detected
Buffalo Web UI	192.168.10.101	http://192.168.10.101	Needs confirmation	Buffalo web interface detected
Cisco Switch	192.168.10.200	ssh 192.168.10.200 / http://192.168.10.200	Needs confirmation	Cisco switch management detected
Tailscale Router	192.168.10.130	ssh 192.168.10.130	Working	Ubuntu / Intel NUC subnet router
VLAN 20 - Servers
Service	IP Address	URL / Access	Status	Notes
Gateway / pfSense VLAN 20	192.168.20.1	https://192.168.20.1:8443	Needs confirmation	Gateway detected with DNS and nginx on port 8443
Proxmox	192.168.20.100	https://192.168.20.100:8006	Working	Proxmox GUI uses HTTPS and port 8006
Server / NAS candidate	192.168.20.102	http://192.168.20.102	Needs confirmation	SMB/NFS ports detected: 139, 445, 2049
Tomcat service host	192.168.20.103	http://192.168.20.103:8080 / https://192.168.20.103:8443	Needs confirmation	Apache Tomcat detected
Linux host	192.168.20.104	ssh 192.168.20.104	Needs confirmation	SSH/RPC detected
Monitoring candidate	192.168.20.105	http://192.168.20.105:9090	Needs confirmation	Go net/http server detected; possible monitoring service
Grafana	192.168.20.108	http://192.168.20.108:3000	Needs confirmation	Grafana detected by nmap
TrueNAS	192.168.20.112	http://192.168.20.112/ui/	Working	TrueNAS GUI requires /ui/ path
Linux / Web host	192.168.20.113	http://192.168.20.113:3000	Needs confirmation	nginx and port 3389 detected
VLAN 30 - Management / iLO / Other
Service	IP Address	URL / Access	Status	Notes
Gateway / pfSense VLAN 30	192.168.30.1	https://192.168.30.1:8443	Needs confirmation	Gateway detected with DNS and nginx on port 8443
Linux host	192.168.30.103	ssh 192.168.30.103	Needs confirmation	Dropbear SSH detected
HP iLO	192.168.30.105	https://192.168.30.105	Needs confirmation	HP Integrated Lights-Out web interface detected
Unknown device	192.168.30.108	Unknown	Needs checking	Ports 53 and 8081 detected

## 5. Servers and Hosts

### Proxmox
- Node name:HP DL380 gen9
- IP:192.168.20.100
- Storage:NAS
- Problems / notes:

### VMs / Containers
Fill one section for each VM or container.

- Name:Openclaw
- IP:
- VLAN:20
- Role:
- System:Linux
- Working correctly? (yes/no):Its working but not as I would like to be work
- Problems / notes: as above

- Name:Prometheus
- IP:
- VLAN:20
- Role:
- System: Linux
- Working correctly? (yes/no):yes
- Problems / notes: I dont see any issue

- Name:Grafana
- IP:
- VLAN:20
- Role:
- System:Linux
- Working correctly? (yes/no):yes
- Problems / notes:no 

- Name:Immich
- IP:
- VLAN:20
- Role:
- System:Linux
- Working correctly? (yes/no):yes
- Problems / notes:no

- Name:
- IP:
- VLAN:
- Role:
- System:
- Working correctly? (yes/no):
- Problems / notes:

- Name:
- IP:
- VLAN:
- Role:
- System:
- Working correctly? (yes/no):
- Problems / notes:

- Name:
- IP:
- VLAN:
- Role:
- System:
- Working correctly? (yes/no):
- Problems / notes:


1. NAS / TrueNAS
- Model:Dell R410
- IP:192.168.20.112
- RAID / pool:
- Problems / notes: This truenas runs jellyfin and nextcloud. No issues 

 2. NAS / TrueNAS
- Model:Dell R730
- IP:192.168.20.
- RAID / pool:
- Problems / notes:

 3. NAS / TrueNAS
- Model: Old PC, CPU Intel I5, Ram drr3 32gb, NIC 10gb
- IP:
- RAID / pool:
- Problems / notes: In futher plans

## 6. DNS and DHCP

### pfSense 
- DHCP enabled? (yes/no):
- DHCP ranges:
- DNS settings:

### Pi-hole
- IP:
- Is it used by all devices? (yes/no):
- Upstream DNS:
- Problems / notes:

## 7. OpenClaw AI
- Where it runs (VM / container / host):VM  
- IP:192.168.20.104
- Role: First idea was just to help me with my homelab, keep updating my homelab reposytorium. Check my emails and calendar
- Resources used (CPU / GPU / RAM):
- File locations:
- Directory structure summary:
- Are soul files organized? (yes/no):yes
- Problems / errors: Quiet often hanging up, when I asked to do something, I got answer that is done, but then after few minutes asking me again about api token or password,
same story is with google account, its running one day and after that i have to go again autoryzation proccess. 

## 8. Storage and Backup
- Where data is stored: NAS
- Backup exists? (yes/no):no
- Backup location:
- Backup frequency:
- Have you tested restore? (yes/no):no

## 9. Monitoring and Logs
- Do you have monitoring? (yes/no):yes
- Tools (e.g. Wazuh, Prometheus):prometheus and Grafana, I want to install Wazuh for servers logs.
- Where logs go: nowhere
- Any alerts? No 

## 10. Global Problems
- Biggest problem: 
- Most chaotic element: Network 
- What are you afraid to change? Im not afraid to change anything, any changes are giving me oportunity to learn more. 
- What breaks most often? Mostly I have probmel with smb on windows, many times I have to map drivers again, sometimes I cant get access , problem with credantials

## 11. Starting Priority
- What do you want to start with: I thnik is worth to check network first 
- Why:
- What should be the first quick win:

## 12. Notes
- Extra notes:
- Open questions:
- Links to diagrams / configs / repo:
