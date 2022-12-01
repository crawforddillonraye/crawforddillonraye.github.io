# crawforddillonraye.github.io
# Docker VPN guide

## obtaining a hosting service
 For this example i ustilized digital ocean to host a vm to run my vpn from but there are other hosting services that you could utilize.
 Step one is to signup for Digital ocean and from here "deploy a container based app"

 Next I chose to deploy an ubuntu server

 You can select a different droplet plan that has your virtual machine have different specs such as increased/decreased  CPU power, storage capacity, memory, and transfer speeds

 I went with the second cheapest droplet  because of the increased choice of hosting locations while also being suitable specs for running my vpn

 Next you can select to use an SSH key which is more secure and reccomended by digial ocean, however for this example i used the password option for ease of writing this guide.

 ## Connecting to and installing docker
  Next take your newly spun up droplet and utilize a program like putty to connect to the ip address of that droplet
Next we need to install docker onto your droplet, i utilized the guide at [This website](https://thematrix.dev/install-docker-and-docker-compose-on-ubuntu-20-04/)
IF you have any issues i reccomend checking their in depth guide, however i will ask that you just copy and paste the commands i'm about to give you.

Install necessary tools
`sudo apt install apt-transport-https ca-certificates curl software-properties-common -y
`
add docker key:
`curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
`
add docker repo
`
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
   `
   Switch to correct repo
   `
   apt-cache policy docker-ce
   `
   install docker
   `
   sudo apt install docker-ce -y
`
Install docker compose
`
sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
`
Set permission
`
sudo chmod +x /usr/local/bin/docker-compose
`

next to install wireguard and setup the docker yml file with the following commands

```
mkdir -p ~/wireguard/
mkdir -p ~/wireguard/config/
nano ~/wireguard/docker-compose.yml

```
  paste the following into the yml file
```
version: '3.8'
services:
  wireguard:
    container_name: wireguard
    image: linuxserver/wireguard
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=America/Toronto
      - SERVERURL=159.203.26.232
      - SERVERPORT=51820
      - PEERS=pc1,pc2,phone1
      - PEERDNS=auto
      - INTERNAL_SUBNET=10.0.0.0
    ports:
      - 51820:51820/udp
    volumes:
      - type: bind
        source: ./config/
        target: /config/
      - type: bind
        source: /lib/modules
        target: /lib/modules
    restart: always
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    sysctls:
      - net.ipv4.conf.all.src_valid_mark=1

     of note in this file is that you should change the timezone to the timezone where your vpn will be hosted, the ip address tot he ip address of your droplet, and the peers will Be the device names to connect to tHE vpn

     next start wireguard by running the following 
     ```
     cd ~/wireguard/
docker-compose up -d
```
## running your vpn on your phone
### ENSURE YOUR WINDOW IS LARGE ENOUGH TO PREVENT TEXT WRAPPING SO THAT THE QR CODE IS READABLE
Next to connect your phone to wireguard run the following
` docker-compose logs -f wireguard
`

Next in the wireguard app on your phone click the little blue plus sign and choose generate from QR code and simply scan the QR code that was generated for phone1 or whatever you decided to name it.
you can test if this is working by visiting
[ipleak.net] (https://ipleak.net/)

## running your vpn on your pc
Next download wireguard from their website at [wireguard.com](https://www.wireguard.com/install/)

Then snag your config file found in ` ~/wireguard/configs/{username} `

and then using wireguard just choose setup a tunnel from a file and then click activate
