# Free5GC Installation Guide

## Preparation

### 1. Change hostname and Network Configuration


a. Open file /etc/hostname
    
    `sudo nano /etc/hostname`
    
    
b. Replace it's contain with "free5gc"

![image](https://hackmd.io/_uploads/ryAxH2GKT.png)

c. Save the file, click "CTRL+x" then "y" then "ENTER"

![image](https://hackmd.io/_uploads/BJnGHhftT.png)



d.  Checkout file under /etc/netplan directory

    `ls /etc/netplan`
    
The command will produce output like 
    
    `01-network-manager-all.yaml`
    
e.  Edit the file
    `sudo nano /etc/netplan/01-network-manager-all.yaml`
    
    Aternatives
    `sudo nano /etc/netplan/$(ls /etc/netplan)`
    
f.  At first, the file will look like
    
   
  ```
network
    version: 2
    renderer: networkd
    ethernets:
        enp0s3:
            dhcp4: no
```
      

Edit the file     
  ```
network:
    version: 2
    renderer: networkd
    ethernets:
    enp0s3:
        dhcp4: no
        addresses: 
            - 192.168.56.101/24
        gateway4: 192.168.56.1
```

![image](https://hackmd.io/_uploads/ry5hH3zYp.png)

g. Check for syntax error in the configuration file

```
sudo netplan try
```

![image](https://hackmd.io/_uploads/ByF-8nfKp.png)

h. Apply the configuration

```
sudo netplan apply
```

![image](https://hackmd.io/_uploads/BkE1L3fFp.png)

## Installation


### 1. Install Golang

a. Download the compressed golang

```
wget https://dl.google.com/go/go1.18.10.linux-amd64.tar.gz
```
![image](https://hackmd.io/_uploads/SJw4tnGF6.png)

Note : If you cannot downloading compressed go file consider to reconfigure network to dynamic addressing using 

```
sudo nano /etc/netplan/$(ls /etc/netplan)
```

change dhcp value to `true`

b. Extract the compressed file

```
sudo tar -C /usr/local -zxvf go1.18.10.linux-amd64.tar.gz
```
![image](https://hackmd.io/_uploads/rJNr9hfY6.png)


c. Create empty directories

```
mkdir -p ~/go/{bin,pkg,src}
```

d. Set some environment variable

```
echo 'export GOPATH=$HOME/go' >> ~/.bashrc
echo 'export GOROOT=/usr/local/go' >> ~/.bashrc
echo 'export PATH=$PATH:$GOPATH/bin:$GOROOT/bin' >> ~/.bashrc
echo 'export GO111MODULE=auto' >> ~/.bashrc
source ~/.bashrc
```

![image](https://hackmd.io/_uploads/rkgR92Gt6.png)



e. Make sure that golang installed properly

```
go version
```

![image](https://hackmd.io/_uploads/Hy77ihfFT.png)


### 2. Install MongoDB


a. Install prerequisities package

```
sudo apt install -y software-properties-common gnupg apt-transport-https ca-certificates 
```

![image](https://hackmd.io/_uploads/BJ7Ps2MFa.png)


b. Import public key for mongodb

```
curl -fsSL https://pgp.mongodb.com/server-7.0.asc |  sudo gpg -o /usr/share/keyrings/mongodb-server-7.0.gpg --dearmor
```

![image](https://hackmd.io/_uploads/S13AonzFa.png)


c. Add mongoDB 7.0 repository to /etc/apt/sources.list.d directory

```
echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-7.0.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list
```

![image](https://hackmd.io/_uploads/rypannMtT.png)


d. Reload local package index

```
sudo apt update
```

![image](https://hackmd.io/_uploads/H13kp2ztT.png)


e. Install mongoDB

```
sudo apt install -y mongodb-org 
```

![image](https://hackmd.io/_uploads/BkZda3GFa.png)


f. Activate mongodb service

```
sudo systemctl start mongod
sudo systemctl enable mongod
```

![image](https://hackmd.io/_uploads/ry65ThGY6.png)


### 4. Install Control-plane, User-plane and Webconsole

a. Install Control-plane dependencies

```
sudo apt -y update
sudo apt install -y wget git
```

![image](https://hackmd.io/_uploads/Sk3RTnGKT.png)


b. Install User-plane dependencies

```
sudo apt -y update
sudo apt -y install git gcc g++ cmake autoconf libtool pkg-config libmnl-dev libyaml-dev
```

![image](https://hackmd.io/_uploads/ryhpC2Mta.png)


c. Edit firewall rule

```
sudo sysctl -w net.ipv4.ip_forward=1
sudo iptables -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE
sudo iptables -A FORWARD -p tcp -m tcp --tcp-flags SYN,RST SYN -j TCPMSS --set-mss 1400
sudo systemctl stop ufw
sudo systemctl disable ufw # prevents the firewall to wake up after a OS reboot
```

![image](https://hackmd.io/_uploads/B1_ZgazKa.png)



Modify the -o (ex. `enp0s3`) options based on the `ifconfig` output

If `ifconfig` command not found, run this command

```
sudo apt install -y net-tools
```

d. Clone control-plane repository

```
cd ~
git clone --recursive -b v3.3.0 -j $(nproc) https://github.com/free5gc/free5gc.git
cd free5gc
```
![image](https://hackmd.io/_uploads/B1lclpzYT.png)



e. Build all network functions

```
make
```
![image](https://hackmd.io/_uploads/r1l3eazKp.png)


f. Retrieve 5G- GTP-U kernel module

```
git clone -b v0.8.3 https://github.com/free5gc/gtp5g.git
cd gtp5g
make
sudo make install
```

![image](https://hackmd.io/_uploads/HkNdZ6Gt6.png)


g. Build the UPF

```
cd ~/free5gc
make upf
```

h. Install nodejs and yarn package

```
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
sudo apt update
sudo apt install -y nodejs yarn
```

i. Build WebConsole

```
cd ~/free5gc
make webconsole
```


## Configuration

### AMF Config

a. Edit file ~/free5gc/config/amfcfg.yaml

```
cd ~/free5gc
sudo nano ./config/amfcfg.yaml
```

b. Find the ngapIpList key

```
...
  ngapIpList:  # the IP list of N2 interfaces on this AMF
  - 127.0.0.1
```

c. Replace localhost address with 192.168.56.101

```
...
  ngapIpList:  # the IP list of N2 interfaces on this AMF
  - 192.168.56.101  # 127.0.0.1
```

d. Save the file and exit from text editor




### SMF Config

a. Edit file ~/free5gc/config/smfcfg.yaml

```
cd ~/free5gc
sudo nano ./config/smfcfg.yaml
```

b. Find interfaces block

```
...
  interfaces: # Interface list for this UPF
   - interfaceType: N3 # the type of the interface (N3 or N9)
     endpoints: # the IP address of this N3/N9 interface on this UPF
       - 127.0.0.8
```

c. Replace address 127.0.0.8 with 192.168.56.101

```
...
  interfaces: # Interface list for this UPF
   - interfaceType: N3 # the type of the interface (N3 or N9)
     endpoints: # the IP address of this N3/N9 interface on this UPF
       - 192.168.56.101  # 127.0.0.8
```


### UPF Config

a. Edit file ~/free5gc/config/upfcfg.yaml

```
cd ~/free5gc
sudo nano ./config/upfcfg.yaml
```

b. Find gtpu IP line

```
...
  gtpu:
    forwarder: gtp5g
    # The IP list of the N3/N9 interfaces on this UPF
    # If there are multiple connection, set addr to 0.0.0.0 or list all the addresses
    ifList:
      - addr: 127.0.0.8
        type: N3
```

c. Replace address 127.0.0.8 with 192.168.56.101

```
...
  gtpu:
    forwarder: gtp5g
    # The IP list of the N3/N9 interfaces on this UPF
    # If there are multiple connection, set addr to 0.0.0.0 or list all the addresses
    ifList:
      - addr: 192.168.56.101
        type: N3

```








