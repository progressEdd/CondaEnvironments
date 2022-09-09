# background

Hello future me, at some point you will make yourself setup wsl because you want 1 terminal to rule them all. You will inevitably run into issues getting your certificates setup in a corporate environment. I hope in your effort to get it setup, you will look for or find this handy document.

WSL does some funky stuff in the background and does apparently does not mirror your own network settings from Windows. You'd think a developer focused company would have figured this out earlier, but alas they haven't. 

# steps

## getting your dns servers setup

When WSL is first setup, it will default to nameservers that don't let you connect to the internet (very boneheaded indeed). To resolve this you will need to go to powershell and execute the following commands. The source is this guide here [WSL2, problem with netowrk connection when VPN used, mvrabel commented on Nov 24, 2021](https://github.com/microsoft/WSL/issues/5068#issuecomment-977826249)

Here's some other helpful links
* [Medium guide](https://medium.com/@aujlanavi0991/windows-subsystem-for-linux-wsl2-override-dns-configuration-ae4207b1a7f7)

If you don't need it for corporate vpn, just run through steps 1,2,3,5. Also I wrote this guide it was kinda fuzzy but I'm pretty sure 1-3, 5 was what I did.

You will need to use a powershell with elevated privalleges for the next set of steps

### 1. in WSL remove your resolv.conf

run the following command

```
sudo rm /etc/resolv.conf
```

### 2. backup your conf
```
sudo cp /etc/resolv.conf /etc/resolv.conf.bk
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

~~### 4. copy your IPV4 addresses of your Ethernet 2 6 IPv4 {X.X.X.X, Y.Y.Y.Y} from powershell~~

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

### 6. apply name changes with chmod
```
sudo chmod 444 /etc/resolv.conf
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
   sudo cp ZscalerRootCA.pem /etc/ssl/certs
   ```

4. update your certificates

   ```
   sudo update-ca-certificates
   ```

4. add it to your path/environment variables. Make sure the certificate filename matches the ones below
   
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

# How YiFei helped me get tabnine working properly
## background
I had a issue where jupyterlab tabnine couldn't download pretrained models, but somehow my laptop was able to pull pip and conda packages. I got my friend YiFei to help me figure out my issue. Turns out I had the correct certificate file, but it wasn't named properly

## steps for how we debugged it

### error outputs
This was the error that kept showing up every time I launched Jupyterlab. I have pasted it here in case somone is searching this same issue
```
[I 2022-04-07 11:15:26.174 ServerApp] jupyterlab_spellchecker extension was successfully loaded. 
install dir: /home/progressedd/miniconda3/lib/python3.9/site-packages/jupyterlab_tabnine 
[D 2022-04-07 11:15:46.202 ServerApp] Traceback (most recent call last) : 
    File "/home/progressedd/miniconda3/lib/python3.9/urllib/request.py", line 1346, in do_open 
        h.request (req.get_method(), req. selector, req. data, headers, 
    File "/home/progressedd/miniconda3/lib/python3.9/http/client.py", line 1279 , in request 
        self._send_request(method, url, body, headers, encode_chunked) 
    File "/home/progressedd/miniconda3/lib/python3.9/http/client.py", line 1325, in _send_request 
        self.endheaders (body, encode_chunked=encode_chunked) 
    File "/home/progressedd/miniconda3/lib/python3.9/http/client. py", line 1274, in endheaders 
        self._send_output (message_body, encode_chunked=encode_chunked) 
    File "/home/progressedd/miniconda3/lib/python3.9/http/client.py", line 1034, in _send_output 
        self.send(msg) File whome/progressedd/miniconda3/Lib/python3.9/http/client.py" line 974, in send self. connect() 
    File "/home/progressedd/miniconda3/lib/python3.9/http/client.py", line 1441, in connect 
        super().connect() 
    File "/home/progressedd/miniconda3/Lib/python3.9/http/client.py",line 945, in connect 
        self.sock = self. _create_connection( 
    File "/home/progressedd/miniconda3/lib/python3.9/socket.py", line 823, in create _connection 
        for res in getaddrinfo (host, port, 0, SOCK_STREAM): 
    File "/home/progressedd/miniconda3/lib/python3.9/socket.py", line 954, in getaddrinfo 
        for res in _socket.getaddrinfo (host, port, family, type, proto, flags): 
    socket.gaierror: [Errno -3] Temporary failure in name resolution 
    
    During handling of the above exception, another exception occurred: 
    Traceback (most recent call last): 
        File "/home/progressedd/miniconda3/Lib/python3.9/site-packages/jupyter_server/extension/manager.py",Line 351, in load extension 
            extension.load_all_points(self.serverapp) 
        File "/home/progressedd/miniconda3/lib/python3.9/site-packages/jupyter_server/extension/manager.py",line 231, in load_all_points 
            return [self. load_point (point_name, serverapp) for point_name in self.extension_points] 
        File "/home/progressedd/miniconda3/lib/python3.9/site-packages/jupyter_server/extension/manager.py",line 231, in <listcomp> 
            return [self. load_point (point_name, serverapp) for point_name in self. extension_points] 
        File "/home/progressedd/miniconda3/lib/python3.9/site-packages/jupyter_server/extension/manager.py",line 224, in load point 
            return point. load(serverapp) 
        File in load home/progressedd/miniconda3/Lib/python3.9/site-packages/jupyter_server/extension/manager.py", line 150, 
            return loader(serverapp) 
        File "home/progresseda/minicondas/Lib/python3.9/site-packages/jupyterlab_tabnine/_init...py",Line 22,in _load_jupyter_server_extension 
            setup_handler(server_app.web_app) 
        File "/home/progressedd/miniconda3/lib/python3.9/site-packages/jupyterlab_tabnine/handler.py",line 28 , in set up_handler 
            tabnine = Tabnine() 
        File "/home/progressedd/miniconda3/lib/python3.9/site-packages/jupyterlab_tabnine/tabnine.py",line104,in init__
            self.download_if_needed() 
        File home/progressedd/miniconda3/Lib/python3.9/site-packages/jupyterlab_tabnine/tabnine.py",line 168, in download_if_needed 
            self._download() 
        File /home/progressedd/miniconda3/Lib/python3.9/site-packages/jupyterlab_tabnine/tabnine.py",line 171, in _download 
            version = get_tabnine_version() 
        File "/home/progressedd/miniconda3/lib/python3.9/site-packages/jupyterlab_tabnine/tabnine.py",line 184, in get_tabnine_version 
            return urlopen(version_url).read().decode("UTF-8").strip() 
        File "/home/progressedd/miniconda3/lib/python3.9/urllib/request.py", line 214, in urlopen 
            return opener. open (url, data, timeout) 
        File "/home/progressedd/miniconda3/lib/python3.9/urllib/request.py", line 517, in open 
            response = self._open (req, data) 
        File "/home/progressedd/miniconda3/lib/python3.9/urllib/request.py", line 534, in _open 
            result = self._call_chain(self. handle_open, protocol, protocol +
        File "/home/progressedd/miniconda3/lib/python3.9/urllib/request.py", line 494, in _call_chain 
            result = func (*args) 
        File "/home/progressedd/miniconda3/lib/python3.9/urllib/request.py", line 1389, in https_open 
            return self. do_open (http. client. HTTPSConnection, req, 
        File "/home/progressedd/miniconda3/lib/python3.9/urllib/request.py", line 1349, in do_open 
            raise URLError(err) 
    urllib.error.URLError: <urlopen error [Errno -3] Temporary failure in name resolution> 
```

The main error to focus on is the `socket.gaierror: [Errno -3] Temporary failure in name resolution` and `urllib.error.URLError: <urlopen error [Errno -3] Temporary failure in name resolution>`, which indicates the issue is in the ssl certificates.

### checking tabnine's script
He had me first check the directory that had the initialization file was erroring out in 

```
cd /home/progressedd/miniconda3/lib/python3.9/site-packages/jupyterlab_tabnine/

nano tabnine.py
```

We found the url that tabnine was trying to access as, `https://update.tabnine.com/bundles/`, but it wasn't accessible by browser or terminal, so we used `https://update.tabnine.com/bundles/version`

#### using curl to access the url
Throwing that into curl, we got the following

```
curl -vv https://update.tabnine.com/bundles/version (base)
* Trying 108.156.120.14:443...
* TCP_NODELAY set
* Connected to update.tabnine.com (108.156.120.14) port 443 (#0)
* ALPN, offering h2
* ALPN, offering http/1.1
* error setting certificate verify locations:
CAfile: /usr/local/share/ca-certificates/ZscalerRootCA.pem
CApath: /etc/ssl/certs
* Closing connection 0
curl: (77) error setting certificate verify locations:
CAfile: /usr/local/share/ca-certificates/ZscalerRootCA.pem
CApath: /etc/ssl/certs
```

#### checking certificate configuration
At that point, he realized there was an issue with the certificate locations. After a quick `history | grep cat` I found my fish config from my history
```
cat ~/.config/fish/config.fish (base)
export CURL_CA_BUNDLE=/usr/local/share/ca-certificates/ZscalerRootCA.pem
export SSL_CERT_FILE=/usr/local/share/ca-certificates/ZscalerRootCA.pem
export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt # ca-bundle.crt
# export REQUESTS_CA_BUNDLE=/usr/local/share/ca-certificates/ZscalerRootCA.pem
```

#### chaning the filename
So he had me move it to rename it using 
```
sudo mv ZscalerRootCertificate.pem ZscalerRootCA.pem
```

#### checking if the applied change worked
then we tried using curl to see if the terminal could connect to the update domain
```
curl -vv https://update.tabnine.com/bundles/version (base)
* Trying 108.156.120.83:443...
* TCP_NODELAY set
* Connected to update.tabnine.com (108.156.120.83) port 443 (#0)
* ALPN, offering h2
* ALPN, offering http/1.1
* successfully set certificate verify locations:
* CAfile: /usr/local/share/ca-certificates/ZscalerRootCA.pem
CApath: /etc/ssl/certs
* TLSv1.3 (OUT), TLS handshake, Client hello (1):
* TLSv1.3 (IN), TLS handshake, Server hello (2):
* TLSv1.3 (OUT), TLS change cipher, Change cipher spec (1):
* TLSv1.3 (OUT), TLS handshake, Client hello (1):
* TLSv1.3 (IN), TLS handshake, Server hello (2):
* TLSv1.3 (IN), TLS handshake, Encrypted Extensions (8):
* TLSv1.3 (IN), TLS handshake, Certificate (11):
* TLSv1.3 (IN), TLS handshake, CERT verify (15):
* TLSv1.3 (IN), TLS handshake, Finished (20):
* TLSv1.3 (OUT), TLS handshake, Finished (20):
* SSL connection using TLSv1.3 / TLS_AES_256_GCM_SHA384
* ALPN, server did not agree to a protocol
* Server certificate:
* subject: CN=tabnine.com
* start date: May 21 06:41:29 2022 GMT
* expire date: Jun 4 06:41:29 2022 GMT
* subjectAltName: host "update.tabnine.com" matched cert's "*.tabnine.com"
* issuer: C=US; ST=California; O=Zscaler Inc.; OU=Zscaler Inc.; CN=Zscaler Intermediate Root CA (zscaler.net) (t)
* SSL certificate verify ok.
> GET /bundles/version HTTP/1.1
> Host: update.tabnine.com
> User-Agent: curl/7.68.0
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< Content-Type: text/plain
< Content-Length: 6
< Connection: keep-alive
< Last-Modified: Sun, 22 May 2022 06:15:37 GMT
< x-amz-version-id: YpweitzJQtn48E7t3edtJys.w8szUcBH
< Server: AmazonS3
< Date: Fri, 27 May 2022 03:05:51 GMT
< ETag: "5f53ea777e060fc77505a637b7a6c692"
< X-Cache: Hit from cloudfront
< Via: 1.1 723f5b4846bb0ac4d05b8fe23c0fa9e0.cloudfront.net (CloudFront)
< X-Amz-Cf-Pop: ORD56-P3
< X-Amz-Cf-Id: AuX71zy_LgNwdiyGkEFvxLEOmvYZVN50hCVqO5CbJkrW_Kpc0x9u-w==
< Age: 59164
<
* Connection #0 to host update.tabnine.com left intact
4.4.12

```
Success! Changing the filename to match the fish config solved the requests error.

# How Yifei helped me fix my requests error
## background
At somepoint, I was trying to use requests for another project, but the kept getting certificate issues
```
Traceback (most recent call last):
  File "/home/progressedd/miniconda3/lib/python3.9/site-packages/urllib3/connectionpool.py", line 703, in urlopen
    httplib_response = self._make_request(
  File "/home/progressedd/miniconda3/lib/python3.9/site-packages/urllib3/connectionpool.py", line 386, in _make_request
    self._validate_conn(conn)
  File "/home/progressedd/miniconda3/lib/python3.9/site-packages/urllib3/connectionpool.py", line 1040, in _validate_conn
    conn.connect()
  File "/home/progressedd/miniconda3/lib/python3.9/site-packages/urllib3/connection.py", line 414, in connect
    self.sock = ssl_wrap_socket(
  File "/home/progressedd/miniconda3/lib/python3.9/site-packages/urllib3/util/ssl_.py", line 449, in ssl_wrap_socket
    ssl_sock = _ssl_wrap_socket_impl(
  File "/home/progressedd/miniconda3/lib/python3.9/site-packages/urllib3/util/ssl_.py", line 493, in _ssl_wrap_socket_impl
    return ssl_context.wrap_socket(sock, server_hostname=server_hostname)
  File "/home/progressedd/miniconda3/lib/python3.9/ssl.py", line 500, in wrap_socket
    return self.sslsocket_class._create(
  File "/home/progressedd/miniconda3/lib/python3.9/ssl.py", line 1040, in _create
    self.do_handshake()
  File "/home/progressedd/miniconda3/lib/python3.9/ssl.py", line 1309, in do_handshake
    self._sslobj.do_handshake()
ssl.SSLCertVerificationError: [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:1129)
During handling of the above exception, another exception occurred:
Traceback (most recent call last):
  File "/home/progressedd/miniconda3/lib/python3.9/site-packages/requests/adapters.py", line 489, in send
    resp = conn.urlopen(
  File "/home/progressedd/miniconda3/lib/python3.9/site-packages/urllib3/connectionpool.py", line 785, in urlopen
    retries = retries.increment(
  File "/home/progressedd/miniconda3/lib/python3.9/site-packages/urllib3/util/retry.py", line 592, in increment
    raise MaxRetryError(_pool, url, error or ResponseError(cause))
urllib3.exceptions.MaxRetryError: HTTPSConnectionPool(host='www.glassdoor.com', port=443): Max retries exceeded with url: /profile/login_input.htm (Caused by SSLError(SSLCertVerificationError(1, '[SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:1129)')))
```
I tried changing my requests certificate bundle from
```
export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt
```
to 
```
export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ZscalerRootCA.pem
```
But that didn't work, so Yifei suggested I generate a strace using `strace -f -s 1024 -ttT -o strace.log`. When he reviewed the log, he highlighted the following
```
6863  14:03:38.183003 openat(AT_FDCWD, "/etc/ssl/certs/ca-certificates.crt", O_RDONLY) = 6 <0.000076>
6863  14:03:27.583332 stat("/home/progressedd/miniconda3/lib/python3.9/site-packages/certifi/cacert.pem", {st_mode=S_IFREG|0664, st_size=285222, ...}) = 0 <0.000032>
```
Which indicated that zscaler was not in the ca-certificates.crt nor cacert.pem. To debug this, I checked if the zcaler certificate was inside of my ca-certs file using the following command 
`cat /etc/ssl/certs/ca-certificates.crt`
When I checked the for the zcaler certificate, it wasn't in ca-certificates.crt. So I tried the open ssl command from stack overflow [Convert .pem to .crt and .key](https://stackoverflow.com/a/60315721)
```
 sudo openssl crl2pkcs7 -nocrl -certfile /usr/local/share/ca-certificates/ZscalerRootCA.pem |
openssl pkcs7 -print_certs -out /usr/local/share/ca-certificates/ZScalerRootCA.crt
```
But the conversion failed and gave me a corrupted value. To get around this, Eric, another sigpwny club member suggested I copy the Zcaler pem and change the file type to .crt since the PEM had the following structure
```
-----BEGIN CERTIFICATE-----
asdjfalsjdflajksdlfkjasdf
-----END CERTIFICATE-----
```
I did it with the following command within `/usr/local/share/ca-certificates/`
```
sudo cp ZscalerRootCA.pem ZscalerRootCA.crt
```
Then I ran 
```
sudo update-ca-certificates --fresh
```
which gave me the critical output
```
Replacing debian:ZscalerRootCA.pem
```
Indicating that Zcaler's certificate was added to my ca-certificates.
