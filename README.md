Pi-hole Home Lab

Home Lab Project: Pi-hole Ad Blocker Setup and Network Privacy Improvement: This is a project that outlines the installation, configuration, and some notes on how to run Pi-hole on an Ubuntu laptop or server as a network-wide ad blocker.

The project was aimed at blocking advertisements and enhancing the performance of the network on a home lab setup.

---

Setup and Installation

1. Update your system

Open a terminal and execute:

  sudo apt update && sudo apt upgrade -y

This ensures that all packages are up to date.

2. Set a static IP-important

Pi-hole works best with devices that have a static IP; otherwise DNS might change after reboot.

Find your network interface:

  ip addr show

Ethernet: enp3s0

Wi-Fi: wlp2s0

2. Create or edit the Netplan config file:

network:
  version: 2
  renderer: NetworkManager
  wifis:
    wlp2s0:
      dhcp4: no
      access-points:
        "<YourWiFiName>":
          password: "<YourWiFiPassword>"
      addresses:
        - <YourStaticIP>/24
      gateway4: <YourGatewayIP>
      nameservers:
        addresses: [1.1.1.1, 8.8.8.8]


4. Apply the Netplan configuration:

  sudo netplan apply

5. If the IP remains dynamic, force static IP using GUI:

  nm-connection-editor

Find your Wi-Fi network → IPv4 Settings → Method = Manual

Enter:

  Address: <YourStaticIP> (same subnet as router, outside DHCP range)

  Netmask: 255.255.255.0

  Gateway: <YourGatewayIP>

Save and reconnect

Check:

  ip addr show <your-interface-name>

You should now see the static IP you assigned.

3. Install Pi-hole

Install curl if needed:

  sudo apt install curl -y

Run the Pi-hole installer:

  curl -sSL https://install.pi-hole.net | bash

During the Setup,

  Network interface: enp3s0 (Ethernet) or wlp2s0 (Wi-Fi)

  Upstream DNS provider: Cloudflare, Google, OpenDNS, etc.

  Web admin interface: Yes (recommended)

  Log/Privacy: Enable logging to allow monitoring, or disable for maximum privacy.

At the end, take note of the Admin URL and default password:

  http://<YourStaticIP>/admin

4. Configure your devices to use Pi-hole

Option A – Router DNS

  Log in to your router

  Set LAN/DHCP DNS server to <YourStaticIP>

  All devices on your network will use Pi-hole automatically

Option B – Manual DNS per device

  Set DNS server to <YourStaticIP> on each device internally (Ubuntu laptop, iPhone etc.

5. Change Pi-hole admin password

Run:

  sudo pihole setpassword

Enter a new password or leave blank to remove it


Troubleshooting:

nslookup pi.hole returns NXDOMAIN: Your device is not using Pi-hole as DNS. Your network/DNS settings should point to <YourStaticIP>.

pihole -a -p displays help menu: Use sudo pihole setpassword instead.

IP still dynamic after Netplan: Check NetworkManager GUI settings and reconnect. What I Learned: How DNS works and how to configure static IPs on Ubuntu Setting up a network-wide ad blocker Troubleshooting of networking and Pi-hole configurations
