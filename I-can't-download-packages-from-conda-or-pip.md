# background
Hello future me, at some point you will make yourself setup wsl because you want 1 terminal to rule them all. You will inevitably run into issues getting your certificates setup in a corporate environment. I hope in your effort to get it setup, you will look for or find this handy document.

WSL does some funky stuff in the background and does apparently does not mirror your own network settings from Windows. You'd think a developer focused company would have figured this out earlier, but alas they haven't. 

# steps 
## getting your dns servers setup
When WSL is first setup, it will default to nameservers that don't let you connect to the internet (very boneheaded indeed). To resolve this you will need to go to powershell and execute the following commands. The source is this guide here [WSL2, problem with netowrk connection when VPN used, mvrabel commented on Nov 24, 2021](https://github.com/microsoft/WSL/issues/5068#issuecomment-977826249)

If you don't need it for corporate vpn, just run through steps 1,2,3,5. Also I wrote this guide it was kinda fuzzy but I'm pretty sure 1-3, 5 was what I did.

You will need to use a powershell with elevated privalleges for the next set of steps
### 1. in WSL remove your resolv.conf
run the following command
```
sudo rm /etc/resolv.conf
```
### 2. edit /etc/wsl.conf 
add the following lines
```
[network]
generateResolvConf = false
```

### 3. shutdown wsl powershell (to edit the settings) 
``` 
wsl --shutdown
```
### 4. copy your IPV4 addresses of your Ethernet 2 6 IPv4 {X.X.X.X, Y.Y.Y.Y} from powershell
```
 Get-DnsClientServerAddress
```
### 5. add the nameservers to your resolv.conf
``` 
nameserver X.X.X.X
nnameserver Y.Y.Y.Y
nameserver 1.1.1.1
nameserver 8.8.8.8
```
### 6. shutdown wsl in powershell (to edit the settings) 
``` 
wsl --shutdown
```
### 7. start up wsl and check the conf again
```
sudo chattr +i resolv.conf
```
### 8. set the adapter in powershell
```
Get-NetAdapter | Where-Object {$_.InterfaceDescription -Match "Cisco AnyConnect"} | Set-NetIPInterface -InterfaceMetric 6000
```
### 9. run a update
```
sudo apt-get update
```

## getting your certificates setup. In my case it was zscaler, which I downloaded from the IT
1. navigate to the directory with all the certificates
2. copy the certificates to ` /usr/local/share/ca-certificates`
```
 sudo cp zscaler.cer /usr/local/share/ca-certificates
 sudo cp ZscalerRootCertificate.pem /usr/local/share/ca-certificates
```
3. copy the certificates to `/etc/ssl/certs `
```
sudo cp ZscalerRootCertificate.pem /etc/ssl/certs
```
4. add it to your path/environment variables
``` 
export CURL_CA_BUNDLE=/usr/local/share/ca-certificates/ZscalerRootCA.pem
export SSL_CERT_FILE=/usr/local/share/ca-certificates/ZscalerRootCA.pem
export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt # ca-bundle.crt
```
5. restart fish
``` 
sudo nano ~/.config/fish/config.fish
```
6. check if the internet works, I was trying to install miniconda
```
wget -v https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
```
