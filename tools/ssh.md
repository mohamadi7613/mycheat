

# SSH
+ openSSH is the most widely used implementation of the SSH protocol. Alternatives are putty, winscp, ...
+ Components of OpenSSH are: ssh, scp, sftp, ssh-keygen, ssh-agent, ...


### ssh basic requirements
```bash
sudo ufw allow ssh
sudo ufw enable
sudo ufw status
sudo apt install openssh-server               # this should be installed on target server
sudo apt install openssh-client               #  this should be installed on controller for connect to servers
sudo systemctl status ssh
```
### Cryptography
+ SSH key pairs consist of a private key and a public key.
+ The private key is kept securely on your local machine, while the public key is shared with remote servers
+ Cryptography is better than passwords since passwords can be guessed or brute-forced

```
[ Public Key ] ---> Encrypt ---> 📨 Message (encrypted)
[ Private Key ] ---> Decrypt ---> 📄 Message (original)
-----------------------
[ Private Key ] ---> Sign ---> 🖊️ Signature
[ Public Key ] ---> Verify ---> ✅ Valid Signature?
```

##### Content of keys

######### 1. Public key format
```
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAICvTccobdF... user@hostname
# 1. Algorithm 2. Key Data (Base64) 3. Comment
```

######### 2. Private key format

```
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAA...
-----END OPENSSH PRIVATE KEY-----
```
types of headers in private-key:
1. PEM  ----> Used for RSA private keys (pre-OpenSSH 6.5)
2. OpenSSH format  ---> Default for SSH private keys (since OpenSSH 6.5)
3. RFC4748  ---> Generic private key format used outside SSH (e.g., OpenSSL)

##### File extension

1. `.pem` (Privacy Enhanced Mail)
    i. .pem is a text-based container using base-64 encoding
    ii. containing 1. Base64 + 2. Headers like: `-----BEGIN EXAMPLE-----`
    iii. File extension for openSSH 
    iv.  public-key, private-key
2. `.ppk`
    a. File extension for PUTTY
    b. Putty Private Key
3. `.csr`
    a. .csr is the certificate request. This is a request for a certificate authority to sign the key.
    b. used by openSSL
4. `.cert` or `.crt`
    a. signed certificates files
    b. used by HTTPS, TLS
5. `.p12`
    a. usually used to combine the private key and certificate.
6. `.key`
    a. for private key

### Fingerprint
+ An `SSH fingerprint` is a short, unique hash of a server’s public SSH key(target) used to verify the identity of a remote server(target)
+ The fingerprint helps verify that you’re connecting to the correct server, not to an attacker
+ When the controller tries to SSH into a target server: `ssh root@192.168.0.1 ` for the first time, the target server sends its public-key to the controller, the finger print will show on the command line with a warning question. (if you specified `passphrase` it also asks passphrase) If you accpet target's public key will store in your machine. If you try to SSH for the second time `ssh root@192.168.0.1` you get `permission denined` because you should specify your private key like `ssh -i mykey.pem root@192.168.0.1` 
+ Common types of fingerprints: 1. MD5 2. SHA256 (default in openSSH)
+ SSH stores its public key fingerprint in a file called: `~/.ssh/known_hosts`


### Passphrase 
+ A passphrase is an extra layer of protection for your private SSH key.
+ passphrase is a password for our private-key
+ every time you try to connect with private-key it asks you about passphrase

### SSH Agent
+ `ssh-agent` is a helper program that stores your private SSH keys in memory after you unlock them with your passphrase.
+ in linux and mac ssh-agent is running by default but in windows we should run it manually from "services"
+ `ssh-add` is a command used to add your private-key to `ssh-agent`, so that you don’t have to enter your passphrase every time.
+ for using ssh-add, ssh-agent should be running

```bash
ssh-add /path/to/private_key     # add a new key which ask about passphrase
ssh-add -l               # list all the keys
ssh-add -D            # remove all keys
```



### SSH Algorithm
+ RSA / DSA / ECDSA / ED25519 are `asymmetric key algorithms` and they are for `encryption & signing` like `SSH keypairs`
+ MD5 / SHA-1 / SHA-256 are `hash functions` and they are for `SSH fingerprints` which means "producing a short hash"


##### SSH algorithms (order of strongest)
1. ED25519 ---> type: Elliptic Curve  ---> default for ssh ---> faster than RSA ---> 256 bits (fixed and not configurable)
2. ECDSA ----> type:  Elliptic Curve DSA ---> fast  ---> size: 256, 384, 521 bits ---> strong if you use 521
3. RSA ---> type: RSA ----> common sizes: 2048 bits (minimum), 3072 bits, 4096 bits (recommended) ---> medium speed
4. DSA ---> type: DSA ----> deprecated ---> 1024 bits (fixed)  ----> fast

##### SSH Hash functions(order of strongest)
1. SHA ---> Secure Hash Algorithm 256 bit ---> SHA1 (160 bits), SHA256 (256 bits)   ---example--> SHA256:y9WqY7pW9vzkT4v+H+UtLsViOcVzF9Hy7FHr1zcyA0E
2. MD5 ---> Message Digest Algorithm 5 ---> 128 bits  ---> deprecated  ---exmpale--> MD5:4c:34:7f:4b:9e:ef:23:7b:6e:5f:6f:7a:b7:2d:4f:e9

+ MD5 generates 32 char in  Hex
+ SHA1 generates 40 char in Hex
+ SHA256 generates 64 char in Hex


```bash
ssh root@192.168.1.10                                  # user@hostname interactively ask for password
ssh root@192.168.1.10 -p 222                                  # -p for port   # default is 22
ssh root@192.168.1.10 'ls -l'                                  # execute a command in remote server
sshpass -p 'your_password' ssh user@hostname            # not recommend to specify your password in command since we can see it with history
ssh root@192.168.1.10 -i key.pem                                  # Use private key with `chmod 600 key.pem`
ssh root@192.168.1.10                                  # if you put your private-key in ./ssh there is no need for -i option
```


### ssh-keygen
+ `known_hosts` is  a file that stores the public keys (fingerprints) of SSH servers you’ve connected to before.

```bash
ssh-keygen                                            # generates key interactively   # creates key pair (public and private)
ssh-keygen -t rsa                                     # -t for type of algorithm: rsa, dsa(deprecated), ecdsa, ...   # ssh-keygen --help
ssh -Q key                                          # list of public algorithms like rsa (the first one is default)
ssh -Q mac                                          # list of Lists HMAC algorithms  (hash algorithms)  like MD5
ssh-keygen -t rsa -b 2048                   # -b for number of bits  # common sizes: 2048(min recommend for rsa), 3072, 4096 # More bits is stronger
ssh-keygen -t rsa -b 2048 -f ./myname                # -f for filename
ssh-keygen -t rsa -b 4096 -C "my comment"           # -c for comment, we can see this comment in fingerprint
ssh-keygen -c -f ./myfile                            # change your comment     # default comment is user@hostname
ssh-keygen -p -f ./myfile                            # change your passphrase   # there is no default for passphrase
ssh-keygen -y -f private.key > public.pub            # Generate public key from private key and redirect it into a file
ssh-keygen -l -E md5 -f ./myfile          # -l or --list means print   # -E for fingerprint_hash
# output:  2048 MD5:aa:bb:cc:dd:ee:ff:00:11:22:33:44:55:66:77:88:99 (RSA)       # print fingerprint of an SSH key file (is equal for both keys)
ssh-copy-id user@hostname                             # Copy public key to server after using ssh-keygen
ssh-keygen -lf ./mykey.pub                           # Check SSH Key Fingerprint
```

##### default file names

+ default location for generated algorithms: `~/.ssh`
+ `id_ed25519` and `id_ed25519.pub` for ED25519 algorithm.
+ `id_rsa` and `id_rsa.pub` for RSA algorithm
+ `id_ecdsa` and `id_ecdsa.pub` for ECDSA algorithm
+ `id_dsa` and `id_dsa.pub` for DSA algorithm


### SSH Config File
+ ssh config helps us from typing long commands over and over
+ file location: `~/.ssh/config`

```bash
Host myserver                      # set a desired name for your host
    HostName 192.168.1.10           # --> "ssh myserver"
    User root
    Port 2222                        # optional
    IdentityFile ~/path/mykey.pem   # key-file path

Host dev*                          # means dev1, dev2, ...
    User developer                # all the hosts with this formula should use this user
Host dev-server1                  # --> "ssh dev-server1"
    Host 192.168.0.2             # User: "developer" apply for this host
```

### Port Forwarding

+ SSH Port Forwarding allows you to securely forward network traffic through an SSH connection.
+ It’s like creating a secure VPN just for a specific port.



### Github SSH











