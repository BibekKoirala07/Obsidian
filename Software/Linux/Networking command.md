Mate, **this is actually a great way to learn Linux networking!** 😄

Instead of learning all the theory first, you'll use commands on your Ubuntu machine, see actual results, and gradually understand what everything means.

For example, you run `ip a`, see your network interfaces, and wonder why one says `lo` and another says `wlp2s0`. That's where real learning begins.

I'm going to give you a **Linux Networking Command Handbook for Ubuntu**, from beginner to advanced. These are actual commands used by Linux administrators, DevOps engineers, backend developers, and network engineers.

You don't need to memorize them. Keep this as your reference and experiment with them.

# **Ubuntu Linux Networking — Command Handbook**

## **1. First, install useful networking tools**

Ubuntu already includes some networking commands, but others require additional packages.

Run:

```
sudo apt update

sudo apt install -y \
  iproute2 \
  iputils-ping \
  dnsutils \
  traceroute \
  net-tools \
  curl \
  wget \
  netcat-openbsd \
  tcpdump \
  nmap \
  mtr-tiny \
  whois \
  ethtool \
  iperf3 \
  nethogs \
  iftop \
  bmon \
  jq
```

These packages give you most of the tools we'll use.

Some commands and services depend on whether you're using Ubuntu Desktop or Ubuntu Server. `nmcli`, `resolvectl`, Netplan, and UFW may already be available.

## **2. Network information commands**

Start here. These commands let you investigate your own Ubuntu machine.

|Command|What it does|
|---|---|
|`ip a`|Show all network interfaces and IP addresses|
|`ip -br a`|Show IP addresses in a shorter format|
|`ip link show`|Show all network interfaces|
|`ip -s link`|Show interface traffic and error counters|
|`hostname -I`|Show your machine's assigned IP addresses|
|`hostname`|Show your computer's hostname|
|`hostnamectl`|Show computer and OS details|
|`ip -4 addr`|Show IPv4 addresses|
|`ip -6 addr`|Show IPv6 addresses|
|`cat /etc/hosts`|View local hostname mappings|
|`cat /etc/resolv.conf`|Inspect resolver configuration|
|`nmcli device status`|Show NetworkManager device status|
|`networkctl status`|Show networkd-related network status|

Try these first:

```
ip a
ip -br a
ip link
hostname -I
```

You'll encounter interfaces such as `lo`, `wlp2s0`, `enp3s0`, and possibly virtual interfaces.

## **3. Internet connectivity commands**

These help you find out whether your network actually works.

|Command|What it does|
|---|---|
|`ping -c 4 google.com`|Test connectivity to Google's domain|
|`ping -c 4 1.1.1.1`|Test connectivity to a public IP|
|`ping -c 4 127.0.0.1`|Test local loopback communication|
|`ping -c 4 192.168.1.1`|Test an example local router address|
|`tracepath google.com`|Show path and path-MTU information|
|`traceroute google.com`|Trace network hops toward a destination|
|`mtr google.com`|Interactive route and packet-loss analysis|
|`curl -I https://example.com`|Request website response headers|
|`curl -v https://example.com`|Show detailed HTTP connection information|
|`wget --spider https://example.com`|Check whether a web resource is reachable|

Note: `192.168.1.1` is only an example. Your gateway might have a different address. Also, some networks and servers block ICMP responses, so failed pings don't always mean internet access is broken.

**Try this:**

```
ping -c 4 google.com
```

Example output:

```
64 bytes from ...: icmp_seq=1 ttl=117 time=20.4 ms
64 bytes from ...: icmp_seq=2 ttl=117 time=21.1 ms
```

You'll start seeing terms like latency, packet loss, TTL, and ICMP.

## **4. IP addresses and routing commands**

Routing determines where your Ubuntu device sends traffic.

|Command|What it does|
|---|---|
|`ip route`|Show IPv4 routing table|
|`ip -6 route`|Show IPv6 routing table|
|`ip route get 8.8.8.8`|Show selected route to an IP|
|`ip route show default`|Show default IPv4 route|
|`ip rule show`|Show routing policy rules|
|`ip neigh show`|Show neighbor/ARP cache|
|`ip -4 neigh`|Show IPv4 neighbor entries|
|`ip route show table all`|Show routes across all routing tables|
|`route -n`|Legacy IPv4 routing table display|
|`arp -n`|Legacy ARP cache display|

Try:

```
ip route
```

Example:

```
default via 192.168.1.1 dev wlp2s0
192.168.1.0/24 dev wlp2s0
```

And:

```
ip route get 8.8.8.8
```

These are among the most useful commands for understanding Linux networking.

## **5. DNS commands**

DNS is responsible for resolving domain names into IP addresses and other records.

|Command|What it does|
|---|---|
|`dig google.com`|Query DNS records|
|`dig google.com A`|Query IPv4 records|
|`dig google.com AAAA`|Query IPv6 records|
|`dig google.com MX`|Query mail server records|
|`dig google.com NS`|Query authoritative nameserver records|
|`dig google.com TXT`|Query TXT records|
|`dig +short google.com`|Show concise DNS answer|
|`dig @1.1.1.1 google.com`|Query a specific DNS resolver|
|`nslookup google.com`|Look up DNS information|
|`host google.com`|Resolve hostname information|
|`resolvectl status`|View system DNS settings|
|`resolvectl query google.com`|Resolve using systemd-resolved|
|`getent hosts google.com`|Resolve using system name service configuration|
|`dig -x 8.8.8.8`|Perform a reverse DNS lookup|

Try:

```
dig google.com
```

Then:

```
dig +short google.com
```

Compare the output.

## **6. Ports, TCP, UDP, and connections**

This is particularly useful for backend development.

|Command|What it does|
|---|---|
|`ss -tuln`|Show listening TCP and UDP sockets|
|`ss -tunap`|Show TCP/UDP sockets with process details|
|`sudo ss -tulpen`|Show detailed listening socket information|
|`ss -tan`|Show TCP sockets and states|
|`ss -uan`|Show UDP sockets|
|`ss -s`|Show socket statistics|
|`ss -ltn`|Show listening TCP sockets|
|`sudo lsof -i`|Show processes using network sockets|
|`sudo lsof -i :3000`|Inspect processes using port 3000|
|`nc -vz 127.0.0.1 3000`|Test a local TCP port|
|`curl http://localhost:3000`|Send a request to a local HTTP server|
|`nc -l 9000`|Start a simple TCP listener|

Install `lsof` if necessary:

```
sudo apt install lsof
```

### **A useful experiment**

Open terminal 1:

```
python3 -m http.server 8000
```

Open terminal 2:

```
ss -ltnp
```

Then:

```
curl http://localhost:8000
```

You're now inspecting a real local HTTP server.

Use `Ctrl+C` in terminal 1 to stop the server. Be aware that Python's HTTP server, when started without an explicit bind address, can listen on non-loopback interfaces. For a strictly local experiment, use `python3 -m http.server 8000 --bind 127.0.0.1`.

## **7. Wi-Fi and NetworkManager commands**

These are particularly useful on Ubuntu Desktop.

|Command|What it does|
|---|---|
|`nmcli general status`|Show NetworkManager status|
|`nmcli device status`|Show network device status|
|`nmcli device wifi list`|List nearby Wi-Fi networks|
|`nmcli connection show`|List connection profiles|
|`nmcli connection show --active`|Show active connections|
|`nmcli device show`|Show detailed device configuration|
|`nmcli radio wifi`|Check whether Wi-Fi radio is enabled|
|`nmcli -f GENERAL,IP4 device show`|Show general and IPv4 device information|
|`iw dev`|Show wireless interface information|
|`iw dev wlp2s0 link`|Show wireless link details for an example interface|
|`rfkill list`|Show wireless radio block status|

Some wireless tools require:

```
sudo apt install iw rfkill
```

For example:

```
nmcli device wifi list
```

This displays nearby Wi-Fi networks, signal strength, security, and other details.

Avoid sharing screenshots containing your home Wi-Fi names, device identifiers, or other personal network details publicly.

## **8. Network speed and bandwidth monitoring**

These are tools you can actually keep using every day.

|Command|What it does|
|---|---|
|`sudo nethogs`|Show bandwidth usage by process|
|`sudo iftop`|Show network traffic between hosts|
|`bmon`|Monitor interface bandwidth|
|`ip -s link`|View traffic and error counters|
|`nstat`|Show kernel network statistics|
|`sar -n DEV 1`|Monitor interface metrics every second|
|`ethtool enp3s0`|Show Ethernet link information|
|`iperf3 -s`|Start a bandwidth-test server|
|`iperf3 -c SERVER_IP`|Measure throughput to an authorized iperf3 server|

The `sar` command requires the `sysstat` package.

For everyday usage, try:

```
sudo nethogs
```

Or:

```
bmon
```

I particularly recommend these two while you're using Ubuntu normally.

## **9. Packet inspection commands**

These are advanced but extremely useful.

|Command|What it does|
|---|---|
|`sudo tcpdump -i any`|Capture packets on available interfaces|
|`sudo tcpdump -i lo`|Capture loopback packets|
|`sudo tcpdump -i any -nn`|Show numeric addresses and ports|
|`sudo tcpdump -i any 'tcp'`|Capture TCP packets|
|`sudo tcpdump -i any 'udp'`|Capture UDP packets|
|`sudo tcpdump -i any 'port 53'`|Capture traffic involving port 53|
|`sudo tcpdump -i lo 'port 8000'`|Inspect local test-server traffic|
|`sudo tcpdump -i any -c 20`|Capture up to 20 packets|

Use packet capture only on systems or networks you're authorized to inspect. Captures can contain sensitive information.

For now, this is enough:

```
sudo tcpdump -i lo -nn -c 20
```

You can learn packet structure later.

## **10. Firewall and security commands**

Ubuntu commonly uses UFW to manage firewall rules.

|Command|What it does|
|---|---|
|`sudo ufw status`|Show UFW status|
|`sudo ufw status verbose`|Show detailed firewall status|
|`sudo ufw status numbered`|Show rules with numbers|
|`sudo nft list ruleset`|Inspect nftables rules|
|`sudo iptables -L -n -v`|Inspect legacy-compatible IPv4 filter rules|
|`systemctl status ufw`|Check UFW service status|

The commands above are inspection commands.

These next examples **modify firewall behavior**, so do not run them just to experiment:

```
sudo ufw allow 22/tcp
sudo ufw allow 443/tcp
sudo ufw deny 8080/tcp
sudo ufw enable
sudo ufw disable
```

Be especially careful when changing a firewall on a remote machine because you can disconnect yourself.

## **11. SSH and remote access**

|Command|What it does|
|---|---|
|`ssh username@SERVER_IP`|Connect to a remote SSH server|
|`ssh -p 2222 username@SERVER_IP`|Connect using another SSH port|
|`ssh -V`|Show SSH client version|
|`ssh -v username@SERVER_IP`|Debug SSH connection establishment|
|`ssh-keygen -t ed25519`|Generate a new SSH key pair|
|`ssh-add -l`|List keys available in SSH agent|
|`scp file.txt username@SERVER_IP:~/`|Copy a file to a remote host|
|`sftp username@SERVER_IP`|Open a secure file-transfer session|
|`systemctl status ssh`|Check SSH server service status|

These are useful when you start working with Ubuntu servers, VPS systems, and cloud infrastructure.

## **12. Network configuration commands**

These commands help you understand how Ubuntu manages network settings.

|Command|What it does|
|---|---|
|`ls /etc/netplan/`|List Netplan configuration files|
|`sudo netplan get`|Inspect Netplan configuration|
|`netplan status`|Display network configuration/status on supported versions|
|`networkctl list`|List networkd-managed interfaces|
|`networkctl status`|Show networkd status|
|`systemctl status NetworkManager`|Inspect NetworkManager service|
|`systemctl status systemd-networkd`|Inspect systemd-networkd service|
|`systemctl status systemd-resolved`|Inspect resolver service|
|`journalctl -u NetworkManager -n 50`|View recent NetworkManager logs|
|`journalctl -u systemd-networkd -n 50`|View recent networkd logs|

Configuration-changing commands:

```
sudo netplan apply
sudo netplan try
sudo systemctl restart NetworkManager
```

Don't run those casually. They can interrupt connectivity.

## **13. Nmap: Network inspection**

Nmap is useful for finding open ports and identifying services on systems you own or are authorized to test.

For example, to inspect your own machine:

```
nmap 127.0.0.1
```

Or check a specific port:

```
nmap -p 8000 127.0.0.1
```

You can compare the output with `ss -ltn` to understand the difference between a local listening socket and how a port appears to a network scanner.

## **14. Linux networking configuration files**

Not everything in Linux networking is a command. Some important information lives in configuration files.

|File or directory|Purpose|
|---|---|
|`/etc/hosts`|Local hostname mappings|
|`/etc/resolv.conf`|Resolver configuration|
|`/etc/nsswitch.conf`|Name service lookup order|
|`/etc/netplan/`|Netplan configuration|
|`/etc/NetworkManager/`|NetworkManager configuration|
|`/etc/systemd/network/`|systemd-networkd configuration|
|`/proc/net/`|Kernel network information|
|`/proc/sys/net/`|Kernel network settings|

To inspect files, use commands such as:

```
cat /etc/hosts
```

```
ls /etc/netplan/
```

```
cat /proc/net/dev
```

```
sysctl net.ipv4.ip_forward
```

The last command checks whether IPv4 forwarding is enabled in the kernel.

# **Your daily networking practice**

You don't have to learn everything at once. Here's a practical routine.

7-DAY PRACTICE PLAN

## **Discover your machine**

Day 1

1234567

Run each command in your Ubuntu terminal, examine the output, and check it off after exploring it.

$ ip a

$ ip -br a

$ hostname -I

$ ip link

Previous

0 / 28 completed

Next day

## **The 10 commands I'd want you to master first**

If you remember only ten commands from this entire guide, make them these:

```
ip a
ip link
ip route
ip route get 8.8.8.8
ping -c 4 google.com
dig google.com
ss -tuln
curl -v https://example.com
nmcli device status
sudo nethogs
```

These give you a solid foundation for investigating your own computer.

**Here's how I suggest we work together:** You run one or two commands on Ubuntu and paste the output here. I'll explain each line in plain English, what it reveals about your device, and why it matters.

For example, start with `ip a`. It's one of the best commands for discovering how networking is actually set up on your machine.


| Command           | Remember it as                      |
| ----------------- | ----------------------------------- |
| `ip a`            | What's my IP address?               |
| `ping google.com` | Can I reach Google?                 |
| `ip route`        | Where does my network traffic go?   |
| `ss -tuln`        | Which ports are open and listening? |