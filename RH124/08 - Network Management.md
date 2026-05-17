### Network Management:  
-----------------------------------------------------------------------------------------------------------------------------

***Network Concepts:***  
***TCP/IP 4-layer model:***  

**Application** → protocols like SSH, HTTPS, FTP, SMTP  
**Transport** → TCP (reliable, connection-oriented) or UDP (fast, connectionless)  
**Internet** → IPv4/IPv6 addressing, routing between networks  
**Link** → physical layer (Ethernet 802.3, WiFi 802.11), uses MAC addresses  

**IP + Port** = socket.   
	Every packet has *source socket* and *destination socket*.  
**cat /etc/services** ---> All well known sockets.  

***Network interface naming (RHEL 7+):***  
	  Names are *persistent* based on *hardware location, not detection order*:  

***Shortnames:***  
**en** = Ethernet, **wl** = WLAN, **ww** = WWAN  
**o#** = onboard device (e.g., eno1)  
**s#** = slot (e.g., enp0s1)  

**lo** → loopback interface, virtual NIC Linux uses to communicate with itself **(localhost)**.  



***IPv4 basics:***  
**IPv4 address** = 32 bits , 4 octets (0-255) separated by dots.  
**Whole address** ---> Network prefix (subnet) + host ID (specific machine).  
**Netmask** ---> defines how many bits are network vs host.  

CIDR notation: /24 = 24 bits for network, 8 bits for hosts  
Decimal notation: 255.255.255.0 (same as /24)  

Special addresses in a subnet:  
**Network address** ---> Host bits all 0.  
**Broadcast address** ---> host bits all 1.  
**Gateway** ---> Rightmost host bit set to 1.   (First usable host)  

**ipcalc <ip>/<prefix>** ---> Know network, broadcast, host range automatically.  


***IPv4 routing:***  
Hosts use a **routing table** to decide *where to send packets*.  
Routers forward packets between networks until they reach the destination.  

***IPv4 configuration:***  
**DHCP** → automatic IP assignment.  
**Static** → manually configured in local config files.  



***IPv6 basics:***  
**IPv6 address = 128 bits** → 8 groups of 4 hex digits, separated by colons (2001:0db8:0000:0010:0000:0000:0000:0001)

***Simplification rules:***
**Drop leading zeros** in each group: 2001:db8:0:10:0:0:0:1  
**Replace consecutive :0: groups with :: only once**: 2001:db8:0:10::1  
**Always lowercase** hex (a-f).  
*Standard subnet mask: /64  (First 64 bits = network, Last 64 bits = interface ID).*  


***Special IPv6 addresses:***  
**::1/128** --> localhost  --------------------- (like 127.0.0.1)  
**::** --> unspecified address  ------------- (like 0.0.0.0)  
**::/0** --> default route  -------------------- (IPv6 internet)  
**fe80::/10** --> link-local  ------------------ (unroutable, auto-configured on every interface)  
**ff00::/8** --> multicast  -------------------- (no broadcast in IPv6)  
**ff02::1** --> all-nodes multicast  -------- (like broadcast)  
**2000::/3** --> global unicast  ------------- (public routable addresses)  
**fd00::/8** --? unique local  ----------------- (like private IPv4, self-allocated)  

*Enclose IPv6 in square brackets when adding a port: [2001:db8:0:10::1]:80*  


***IPv6 configuration:***  

**Manual** → static assignment (avoid reserved)  
**DHCPv6** → like DHCP but uses multicast instead of broadcast.  
**SLAAC (Stateless Address Autoconfiguration)** → host sends router solicitation to ff02::2, router replies with network prefix, host builds its own address from prefix + auto-generated interface ID.
*SLAAC is default for IPv6 when configured for dynamic addressing.*


**DNS** → Mapping hostnames to IPs.  
**/etc/resolv.conf** ---> Has all defined DNS servers.  
**/etc/hosts** → Has maps from IP to hostname .  


***Gathering Network Info:***  
**ip link show** → List connnected NICs  
**ip -br link** → Brief summary for each NIC.  
**ip a s** → Show IP v4 and v6 and MAC addresses for each interface.  
**ip -br a** → One line summary of all NICs IPs.  
**ip -s link show <NIC>** → Show interface statistics.  
**ip route** → Display IPv4 routing table.  
**ip -6 route** → Display IPv6 routing table.  


***Testing Connectivity:***  
**ping <ipv4>** → Test IPv4 connectivity.  
**ping -c3 <ipv4>** → Send only 3 packets.  
**ping6 <ipv6>** → Test IPv6 connectivity.  
**ping6 <ipv6>%ens4**→ Ping address via an NIC (specify interface with %).  
**ping6 ff02::1%ens4** → Discover all IPv6 hosts on local network.  
  
**ssh <ipv6>%ens4** ---> ssh using ipv6.  


**Tools to Trace Routes:**  
**tracepath <ipv4>** → Trace network path to destination, **showing each router hop.**  
**tracepath6 <ipv6>** → IPv6 version.  
**asymm flag** = traffic returned via a different route than it went out.  

**mtr <host>** → Trace statistics for packets sent, can generate report after finishing.  


**Checking Open Ports & Services that se them:**  
**ss -punt** → Show listening TCP/UDP sockets with process info.  
**ss -lt** → Show listening TCP sockets with service names.  

**SS Options:**  
**-t** → TCP sockets  
**-u** → UDP sockets  
**-l** → listening sockets only  
**-a** → all sockets (listening + established)  
**-n** → numeric (no name resolution)  
**-p** → show process using the socket  
**-A inet** → IPv4 only  

Socket states in output:  
**LISTEN** → Service waiting for connections.  
**ESTAB** → Active connection established.  

  

### ***NetworkManager (nmcli / nmtui):***  
---
**nmcli c s** → Show all network profiles.  
**nmcli c s "Profile"** → Show config files for that profile.  
**nmcli dev show "NIC"** ---> Shw nm config file for an nm profile.  
**nmcli dev status** ---> show connected/disconnected NICs.  
**nmcli con add con-name <name> type ethernet ifname <interface>** → Add a new profile.  
**nmcli c delete "Profile"** --> Delete a nm profile.  
**nmcli -f IP4 dev show "NIC"** --> Fetch ipv4 of a NIC directly from its config files.  
**nmcli con mod <profile> <property> <value>** → Modify profile settings.  
**nmcli con reload** → Apply profile modifications.  
**nmcli con up <profile>** → Activate a profile.  
**nmcli con down <profile>** → Deactivate a profile.  

**nmtui** → Text UI for NetworkManager.  


***Hostname & Name Resolution:***  
**hostname** → Show current (temp) hostname.  
**hostname <newhostname>** → Set hostname temporarily (lost on reboot).  
**hostnamectl hostname** → Show persistent hostname.  
**hostnamectl hostname <newhostname>** → Set persistent hostname (survives reboot, writes to /etc/hostname).  

**/etc/nsswitch.conf** ---> Controls name resolution order.  

**/etc/hosts** → static hostname-to-IP mappings:  
```
127.0.0.1       localhost
172.25.254.254  xcatmn.cluster.com xcatmn
```
**/etc/resolv.conf** → DNS client configuration.  
			- **search** → domain suffixes to append to short hostnames  
			- **nameserver** → DNS server IPs (up to 3 for redundancy)  
```
search cluster.com
nameserver 172.25.250.100
nameserver 172.25.254.254
```


***Configuring DNS via NetworkManager:***  
**nmcli con mod '>profile>' ipv4.dns >ip>** → Set DNS server (overwrites existing).  
**nmcli con mod '>profile>' +ipv4.dns >ip>** → Add a DNS server.  
**nmcli con mod '>profile>' -ipv4.dns >ip>** → Remove a DNS server.  
**nmcli con mod '>profile>' ipv6.dns >ipv6>**> → Set IPv6 DNS server.  


**nmcli con mod '>profile>' ipv4.ignore-auto-dns yes** ---> Prevent DHCP from overwriting DNS settings in /etc/resolv.conf.  

***Testing DNS resolution:***   
**host <hostname>** → Resolve hostname to IP (queries DNS directly, ignores /etc/hosts  
**host <ip>** → Reverse DNS lookup (IP to hostname).  
**dig <hostname>** → Shows query details, response time, DNS server used.  
**getent hosts <hostname>** → Test resolution using the system's normal resolver (checks /etc/hosts first, then DNS — same behavior as applications).  

*Use getent hosts to test how applications will actually resolve names.*  
*Use host or dig to test DNS servers directly.*  



Thank you :)










                 
