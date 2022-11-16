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
![image](https://user-images.githubusercontent.com/70538441/202269212-035c5e1f-dcef-40f9-894e-ecc6915a1673.png)
8. Navigate to the config directory and retrieve the conf file for peer_pc1. Absolute path was: `wireguard/config/peer_pc1/peer_pc1.conf`
![image](https://user-images.githubusercontent.com/70538441/202269377-1565e0d9-8e66-4341-a482-20ccdfccd75c.png)
9. Copy this into a conf file on my local machine. Import it into wireguard. 
![image](https://user-images.githubusercontent.com/70538441/202269434-162421a6-b978-449a-9b4c-f1a742416c29.png)
10. Connect and navigate to ipleak.net to ensure I have a NJ IP address
![image](https://user-images.githubusercontent.com/70538441/202269540-44a65606-03be-4be6-b0cc-4d530b3eb0f9.png)
11. Connect with WinSCP and open `wireguard/config/peer_phone1/peer_phone1.png` and scan the QR code with wireguard app on phone.
![image](https://user-images.githubusercontent.com/70538441/202269886-7dd0fe68-e8ea-4374-95fc-1cf2e239f8f9.png)
12. Navigate to ipleak.net to ensure I have the correct address on my phone.
![IMG_5422](https://user-images.githubusercontent.com/70538441/202270242-ac3eaa45-fd8b-4264-9608-0aabc03fa5bc.PNG)

