# Hetzner Proxmox IP setup
Since I myself have searched far too long to correctly set my AP addresses at hetzner I wanted to give a small guide for it here.

## Proxmox Host Settings
First you have to change some settings in `/etc/network/interfaces`. (My config looks like this)
```conf
auto lo
iface lo inet loopback

# device: enp1s0
auto enp5s0
iface enp5s0 inet static
  address <Host IPv4>
  netmask 255.255.255.255
  pointopoint <Host gateway>
  gateway <Host gateway>

iface enp5s0 inet6 static
  address  <Host IPv6>2   # like 2001:db8::2
  netmask 128
  gateway fe80::1
  up sysctl -p

auto vmbr0
iface vmbr0 inet static
  address <Host IPv4>
  netmask 255.255.255.255
  bridge_ports none
  bridge_stp off
  bridge_fd 0
  sysctl -w net.ipv4.ip_forward=1
  sysctl -w net.ipv6.conf.all.forwarding=1
  up ip route add <Additional single IP 1>/32 dev vmbr0
  up ip route add <Additional single IP 2>/32 dev vmbr0
  up ip route add <Additional single IP 3>/32 dev vmbr0
  up ip route add <Additional single IP 4>/32 dev vmbr0
  up ip route add <Additional single IP 5>/32 dev vmbr0
  up ip route add <Additional single IP 6>/32 dev vmbr0



iface vmbr0 inet6 static
  address  <Host IPv6>1   # like 2001:db8::1
  netmask 64
```
![image](https://user-images.githubusercontent.com/79027536/226149387-35b549a8-1a84-42a0-a57f-7c2c6a8cef59.png)
![image](https://user-images.githubusercontent.com/79027536/226149409-a2541efd-b38f-4446-8274-a87d2f481f63.png)

Host IPv4 = is the lowest ip address without the extra symbol next to it <br>
Host gateway = is the ip under gateway when hovering over the ip address <br>
With the ip address you have to be careful that you change the end because otherwise this does not work like in the example my ips are "0000:0000:0000:49b::2" and "0000:0000:0000:49b::1".

## VM/CT config
In proxmox for the VM via cloudinit you have to specify this:
![image](https://user-images.githubusercontent.com/79027536/226149977-c6555995-82d9-42e8-9767-d820e6ecf65e.png)
just keep in mind that the gateway for the Additional single IP from now on will be the main ips you specified for vmbr0.
