---
layout: page
title: "VPN Project"
permalink: /vpnProject
---

Spinning up Wireguard...
1. Setup Digital Ocean ubuntu droplet. I chose an image with docker preinstalled from the Marketplace.
2. Make a new directory on the droplet entitled "wireguard".
3. Within the directory, create a "config" directory.
4. Obtain docker compose file from: `https://thematrix.dev/setup-wireguard-vpn-server-with-docker/`. Copy it into the file on the droplet.
5. Change TZ to America/Chicago in the compose file.
6. Change SERVERURL to my public IP. `165.227.86.72`
7. Run `sudo docker-compose up -d` to start wireguard in a detatched state. Run docker ps to ensure that it's up.
8. Navigate to the config directory and retrieve the conf file for peer_pc1. Absolute path was: `wireguard/config/peer_pc1/peer_pc1.conf`
PICTURE HERE
9. Copy this into a conf file on my local machine. Import it into wireguard. 
PICTURE HERE
10. Connect and navigate to ipleak.net to ensure I have a NJ IP address
PICTURE HERE
11. Open `wireguard/config/peer_phone1/peer_phone1.png` and scan the QR code with wireguard app on phone.
12. Navigate to ipleak.net to ensure I have the correct address on my phone.
PICTURE HERE
