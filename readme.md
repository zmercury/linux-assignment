# Linux Assignment

Building a small company network inside virtual machine

**CentOS machine acts as the server**
**Ubuntu machine acts as the client**

> Client will connect to the server and use it services

```
                    VirtualBox Network
                           │
                           │
        ┌──────────────────┴──────────────────┐
        │                                     │
┌───────────────┐                     ┌────────────────┐
│  CentOS VM    │                     │   Ubuntu VM    │
│   (SERVER)    │                     │   (CLIENT)     │
│               │                     │                │
│ DNS Server    │                     │ Test DNS       │
│ Email Server  │                     │ Send Emails    │
│ Web Server    │                     │ Access Website │
│ SSL/TLS       │                     │ Thunderbird    │
│ Extra Service │                     │ Test Services  │
└───────────────┘                     └────────────────┘
```

## 🔧 Pre-Setup: VirtualBox Network Configuration

Before anything else, both VMs need to communicate with each other.

**In VirtualBox, for BOTH VMs:**

- Go to **Settings → Network**
- Set **Adapter 1** to: `NAT` (for internet access)
- Set **Adapter 2** to: `Host-Only Adapter` (so they talk to each other)

## 📋 Initial Setup on CentOS Server

First, log into your CentOS server and run these foundational commands:

```bash
# Update the system
sudo dnf update -y

# Check your IP address (note down the Host-Only adapter IP, usually 192.168.56.x)
ip addr show

# Set your hostname (replace 'groupname' with YOUR actual group name)
sudo hostnamectl set-hostname server.lhotse.com

# Disable firewall temporarily while configuring (re-enable properly later)
sudo systemctl stop firewalld
sudo systemctl disable firewalld

# Disable SELinux temporarily (optional but saves headaches)
sudo setenforce 0
sudo sed -i 's/SELINUX=enforcing/SELINUX=permissive/' /etc/selinux/config
```

> _If it doesn't show Ip address in host only adapter run this to get an Ip_

# Fix Option 1 (Recommended): Use Network Manager

Most modern Linux systems use **Network Manager**.

Try:

```bash
nmcli device status
```

Then request an IP for the host-only adapter:

```bash
sudo nmcli device connect enp0s8
```

Then check again:

```bash
ip addr show
```

**Host Only IP Address (mero pc ko): `192.168.56.101`**

## ✅ TASK 1: DNS Server using BIND

### On CentOS Server

**Step 1 — Install BIND**

```bash
sudo dnf install bind bind-utils -y
```

**Step 2 — Configure the main BIND config file**

````bash
sudo nano /etc/named.conf
```

Replace or edit the file to look like this (replace `192.168.56.101` with your CentOS Host-Only IP and `groupname` with your group name):
```
options {
    listen-on port 53 { 127.0.0.1; 192.168.56.101; };
    listen-on-v6 port 53 { ::1; };
    directory       "/var/named";
    dump-file       "/var/named/data/cache_dump.db";
    statistics-file "/var/named/data/named_stats.txt";
    memstatistics-file "/var/named/data/named_mem_stats.txt";
    allow-query     { localhost; 192.168.56.0/24; };
    recursion yes;
    forwarders { 8.8.8.8; 8.8.4.4; };  // forwards unknown queries to Google DNS
    dnssec-validation yes;
};

zone "lhotse.com" IN {
    type master;
    file "/var/named/lhotse.com.zone";
    allow-update { none; };
};

zone "56.168.192.in-addr.arpa" IN {
    type master;
    file "/var/named/lhotse.com.rev";
    allow-update { none; };
};
````

**Step 3 — Create the Forward Zone File**

````bash
sudo nano /var/named/lhotse.com.zone
```
```
$TTL 86400
@   IN  SOA     server.lhotse.com. admin.lhotse.com. (
                2026031101  ; Serial
                3600        ; Refresh
                1800        ; Retry
                604800      ; Expire
                86400 )     ; Minimum TTL

; Name servers
@       IN  NS      server.lhotse.com.

; A Records
server  IN  A       192.168.56.101
client  IN  A       192.168.56.102

; Mail record
@       IN  MX  10  server.lhotse.com.
````

**Step 4 — Create the Reverse Zone File**

````bash
sudo nano /var/named/lhotse.com.rev
```
```
$TTL 86400
@   IN  SOA     server.lhotse.com. admin.lhotse.com. (
                2026031101
                3600
                1800
                604800
                86400 )

@       IN  NS      server.lhotse.com.

; PTR Records (last octet of IP → hostname)
101     IN  PTR     server.lhotse.com.
102     IN  PTR     client.lhotse.com.
````

**Step 5 — Set correct permissions and start BIND**

```bash
sudo chown named:named /var/named/lhotse.com.zone
sudo chown named:named /var/named/lhotse.com.rev

# Check syntax before starting
sudo named-checkconf
sudo named-checkzone lhotse.com /var/named/lhotse.com.zone
sudo named-checkzone 56.168.192.in-addr.arpa /var/named/lhotse.com.rev

# Start and enable BIND
sudo systemctl start named
sudo systemctl enable named
sudo systemctl status named
```

**Step 6 — Open firewall for DNS**

Make sure to enable the firewall that was disabled earlier

```bash
sudo systemctl start firewalld
sudo systemctl enable firewalld
```

then do following:

```bash
sudo firewall-cmd --permanent --add-service=dns
sudo firewall-cmd --reload
```

**Step 7 — Test DNS on the server itself**

```bash
nslookup server.lhotse.com 127.0.0.1
nslookup client.lhotse.com 127.0.0.1
nslookup google.com 127.0.0.1   # tests external resolution (forwarder)
dig server.lhotse.com
dig -x 192.168.56.101
```

### On Ubuntu Client — Configure to use your DNS server

````bash
# Edit the DNS resolver config
sudo nano /etc/systemd/resolved.conf
```
Add/change these lines:
```
[Resolve]
DNS=192.168.56.101
Domains=lhotse.com
````

> You have to uncomment the DNS and Domain and add your Ip and your group link

📸 **Screenshot opportunities:** `nslookup` and `dig` results, `ping` working, both forward and reverse lookups.

## ✅ TASK 2: Email Server (Postfix + Dovecot + SSL/TLS)

### On CentOS Server

**Step 1 — Install Postfix and Dovecot**

```bash
sudo dnf install postfix dovecot -y
sudo dnf install mailx -y    # command line mail tool for testing
```

**Step 2 — Create a self-signed SSL certificate for email**

```bash
sudo mkdir -p /etc/ssl/lhotse
cd /etc/ssl/lhotse

sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout mail.lhotse.com.key \
    -out mail.lhotse.com.crt \
    -subj "/C=NP/ST=Bagmati/L=Kathmandu/O=Lhotse/CN=mail.lhotse.com"

sudo chmod 600 mail.lhotse.com.key
```

**Step 3 — Configure Postfix**

````bash
sudo nano /etc/postfix/main.cf
```

Find and modify (or add) these lines:
```
myhostname = server.lhotse.com
mydomain = lhotse.com
myorigin = $mydomain
inet_interfaces = all
mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain
mynetworks = 127.0.0.0/8, 192.168.56.0/24
home_mailbox = Maildir/

# TLS settings
smtpd_tls_cert_file = /etc/ssl/lhotse/mail.lhotse.com.crt
smtpd_tls_key_file = /etc/ssl/lhotse/mail.lhotse.com.key
smtpd_use_tls = yes
smtpd_tls_security_level = may
smtp_tls_security_level = may
smtpd_sasl_type = dovecot
smtpd_sasl_path = private/auth
smtpd_sasl_auth_enable = yes
smtpd_recipient_restrictions = permit_sasl_authenticated, permit_mynetworks, reject_unauth_destination
````

**Step 4 — Enable SMTPS/Submission port in Postfix**

bash

````bash
sudo nano /etc/postfix/master.cf
```

Uncomment these lines (remove the `#`):
```
submission inet n       -       n       -       -       smtpd
  -o syslog_name=postfix/submission
  -o smtpd_tls_security_level=encrypt
  -o smtpd_sasl_auth_enable=yes
  -o smtpd_recipient_restrictions=permit_sasl_authenticated,reject
````

**Step 5 — Configure Dovecot**

bash

````bash
sudo nano /etc/dovecot/dovecot.conf
```
Add or confirm:
```
protocols = imap pop3 lmtp
````

bash

````bash
sudo nano /etc/dovecot/conf.d/10-mail.conf
```
Change:
```
mail_location = maildir:~/Maildir
````

bash

````bash
sudo nano /etc/dovecot/conf.d/10-auth.conf
```
Change:
```
disable_plaintext_auth = no
auth_mechanisms = plain login
````

bash

````bash
sudo nano /etc/dovecot/conf.d/10-ssl.conf
```
Change:
```
ssl = yes
ssl_cert = </etc/ssl/lhotse/mail.lhotse.com.crt
ssl_key = </etc/ssl/lhotse/mail.lhotse.com.key
````

bash

````bash
sudo nano /etc/dovecot/conf.d/10-master.conf
```

Inside the `service auth` block, add/uncomment:
```
unix_listener /var/spool/postfix/private/auth {
    mode = 0660
    user = postfix
    group = postfix
}
````

**Step 6 — Create test users**

bash

```bash
sudo useradd -m user1
sudo passwd user1   # set a password, e.g. Password123

sudo useradd -m user2
sudo passwd user2

# Create Maildir structure for each user
sudo -u user1 mkdir -p /home/user1/Maildir/{new,cur,tmp}
sudo -u user2 mkdir -p /home/user2/Maildir/{new,cur,tmp}
```

**Step 7 — Start services and open firewall**

bash

```bash
sudo systemctl start postfix dovecot
sudo systemctl enable postfix dovecot
sudo systemctl status postfix
sudo systemctl status dovecot

# Open firewall ports
sudo firewall-cmd --permanent --add-service=smtp
sudo firewall-cmd --permanent --add-service=smtps
sudo firewall-cmd --permanent --add-service=imap
sudo firewall-cmd --permanent --add-service=imaps
sudo firewall-cmd --permanent --add-service=pop3
sudo firewall-cmd --permanent --add-service=pop3s
sudo firewall-cmd --permanent --add-port=587/tcp
sudo firewall-cmd --reload
```

**Step 8 — Test email from command line first**

bash

```bash
# Send a test email from user1 to user2
echo "Hello from user1" | mail -s "Test Email" user2@lhotse.com

# Check if user2 received it
sudo ls /home/user2/Maildir/new/
```

### On Ubuntu Client — Set up Thunderbird

```bash
sudo apt install thunderbird -y
```

**In Thunderbird:**

1. Click **"Set up an existing email"**
2. Enter: Name → `User One`, Email → `user1@lhotse.com`, Password → your password
3. Click **Configure Manually** and set:
   - **Incoming (IMAP):** `192.168.56.101`, Port `993`, SSL/TLS
   - **Outgoing (SMTP):** `192.168.56.101`, Port `587`, STARTTLS
4. Accept the self-signed certificate warning
5. Do the same for `user2@lhotse.com`
6. Send an email from user1 to user2 and verify it arrives

📸 **Screenshot:** Thunderbird account setup, sent email, received email in inbox, SSL certificate info.

## ✅ TASK 3: Apache Web Server with HTTPS

### On CentOS Server

**Step 1 — Install Apache and SSL module**

```bash
sudo dnf install httpd mod_ssl -y
```

**Step 2 — Create SSL certificate for the web server**

```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout /etc/ssl/lhotse/web.lhotse.com.key \
    -out /etc/ssl/lhotse/web.lhotse.com.crt \
    -subj "/C=NP/ST=Bagmati/L=Kathmandu/O=Lhotse/CN=www.lhotse.com"
```

**Step 3 — Create two websites (for Credit/Distinction marks)**

Website 1:

```bash
sudo mkdir -p /var/www/site1
sudo nano /var/www/site1/index.html
```

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Lhotse - Site 1</title>
  </head>
  <body>
    <h1>Welcome to Lhotse Main Website</h1>
    <p>Server: server.lhotse.com | Secure HTTPS connection</p>
  </body>
</html>
```

Website 2:

```bash
sudo mkdir -p /var/www/site2
sudo nano /var/www/site2/index.html
```

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Lhotse - Site 2</title>
  </head>
  <body>
    <h1>Lhotse Secondary Website</h1>
    <p>This is a second virtual host on server.lhotse.com</p>
  </body>
</html>
```

**Step 4 — Create Virtual Host config files**

```bash
sudo nano /etc/httpd/conf.d/site1.conf
```

```apache
<VirtualHost *:80>
    ServerName www.lhotse.com
    DocumentRoot /var/www/site1
    Redirect permanent / https://www.lhotse.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName www.lhotse.com
    DocumentRoot /var/www/site1

    SSLEngine on
    SSLCertificateFile /etc/ssl/lhotse/web.lhotse.com.crt
    SSLCertificateKeyFile /etc/ssl/lhotse/web.lhotse.com.key

    <Directory /var/www/site1>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```

```bash
sudo nano /etc/httpd/conf.d/site2.conf
```

```apache
<VirtualHost *:80>
    ServerName portal.lhotse.com
    DocumentRoot /var/www/site2
    Redirect permanent / https://portal.lhotse.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName portal.lhotse.com
    DocumentRoot /var/www/site2

    SSLEngine on
    SSLCertificateFile /etc/ssl/lhotse/web.lhotse.com.crt
    SSLCertificateKeyFile /etc/ssl/lhotse/web.lhotse.com.key

    <Directory /var/www/site2>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```

**Step 5 — Add DNS entries for the new hostnames**

````bash
sudo nano /var/named/lhotse.com.zone
```
Add these lines:  A Record ma halne
```
www     IN  A   192.168.56.101
portal  IN  A   192.168.56.101
````

Then update the serial number and restart BIND:

```bash
sudo systemctl restart named
```

**Step 6 — Start Apache**
you might get an error here, if you do

```bash
# open apche ssl config
sudo vim /etc/httpd/conf.d/ssl.conf

# find these line
SSLCertificateFile /etc/pki/tls/certs/localhost.crt
SSLCertificateKeyFile /etc/pki/tls/private/localhost.key

# replace with the one you made earlier
SSLCertificateFile /etc/ssl/lhotse/web.lhotse.com.crt
SSLCertificateKeyFile /etc/ssl/lhotse/web.lhotse.com.key

# test now
sudo apachectl configtest
```

```bash
# Test config syntax
sudo apachectl configtest

sudo systemctl start httpd
sudo systemctl enable httpd
sudo systemctl status httpd

# Open firewall
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --reload
```

---

### On Ubuntu Client — Test the websites

```bash
# Open Firefox and go to:
# https://www.groupname.com
# https://portal.groupname.com

# Accept the self-signed certificate warning in Firefox
# You can also test from terminal:
curl -k https://www.lhotse.com
curl -k https://portal.lhotse.com
```

📸 **Screenshot:** Both websites loading in Firefox with the padlock icon (HTTPS), certificate details.

## ✅ TASK 4: Extra Service — FTP Server (vsftpd)

A great choice for your extra service is **vsftpd** (Very Secure FTP Daemon) — it's well-documented, practical, and easy to demonstrate from the Ubuntu client.

### On CentOS Server

**Step 1 — Install vsftpd**

```bash
sudo dnf install vsftpd -y
```

**Step 2 — Configure vsftpd**

````bash
sudo nano /etc/vsftpd/vsftpd.conf
```

Find and set these options:
```
anonymous_enable=NO
local_enable=YES
write_enable=YES
local_umask=022
dirmessage_enable=YES
xferlog_enable=YES
connect_from_port_20=YES
xferlog_std_format=YES
listen=YES
listen_ipv6=NO
pam_service_name=vsftpd
userlist_enable=YES
userlist_deny=NO
userlist_file=/etc/vsftpd/user_list

# Passive mode settings (important for VirtualBox NAT)
pasv_enable=YES
pasv_min_port=40000
pasv_max_port=50000

# Chroot users to their home directory for security
chroot_local_user=YES
allow_writeable_chroot=YES
````

**Step 3 — Add users to the FTP allowed list**

```bash
echo "user1" | sudo tee -a /etc/vsftpd/user_list
echo "user2" | sudo tee -a /etc/vsftpd/user_list
```

**Step 4 — Start and enable**

```bash
sudo systemctl start vsftpd
sudo systemctl enable vsftpd

# Firewall
sudo firewall-cmd --permanent --add-service=ftp
sudo firewall-cmd --permanent --add-port=40000-50000/tcp
sudo firewall-cmd --reload
```

### On Ubuntu Client — Test FTP

```bash
# Install FTP client
sudo apt install ftp filezilla -y

# Test via command line
ftp 192.168.56.101
# Login with user1 / password
# Try: ls, put testfile.txt, get testfile.txt

# Or open FileZilla:
# Host: 192.168.56.101 | User: user1 | Password: Password123 | Port: 21
```

📸 **Screenshot:** FileZilla connected, file transfer in both directions.

## Section 5: Troubleshooting Issues Encountered

### 5. Troubleshooting Issues Encountered and Solutions

During the installation and configuration of the DNS server, email server, web server, and additional service, several technical issues were encountered. The following section describes the major problems, how they were identified, and the steps taken to resolve them.

### 5.1 Dovecot Service Failure Due to Port Conflict

#### Problem

While starting the Dovecot service, the server failed to start and produced the following error:

```bash
Error: service(submission-login): listen(*, 587) failed: Address already in use
Fatal: Failed to start listeners
```

#### Cause

Port **587** was already being used by the **Postfix SMTP submission service**, causing a conflict with Dovecot attempting to listen on the same port.

#### Solution

The Dovecot configuration files were inspected using:

```
grep -R "submission" /etc/dovecot
```

The unnecessary **submission-login service configuration** was commented out in the Dovecot configuration file.

After modifying the configuration, the service was restarted:

```
sudo systemctl restart dovecot
```

The service then started successfully.

### 5.2 Email Delivery Failure Due to Incorrect Domain

#### Problem

When attempting to send emails using the `mail` command, the messages remained in the mail queue and were not delivered.

Example output:

```bash
(connect to groupname.com:25: Connection refused)
```

#### Cause

The email was being sent to a domain that did not exist within the locally configured mail server. The mail server attempted to deliver the email externally instead of locally.

#### Solution

The **Postfix configuration file** `/etc/postfix/main.cf` was reviewed and corrected to include the correct domain configuration:

```bash
myhostname = server.lhotse.com
mydomain = lhotse.com
mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain
```

After updating the configuration, Postfix was restarted:

```
sudo systemctl restart postfix
```

Emails were then delivered successfully to local users.

### 5.3 Thunderbird Email Sending Failure (STARTTLS Error)

#### Problem

While sending emails using Thunderbird from the Ubuntu client, the following error occurred:

```bash
Must issue a STARTTLS command first
```

#### Cause

The SMTP server required encrypted communication, but the Thunderbird client configuration did not correctly initiate **STARTTLS encryption**.

#### Solution

The SMTP configuration in Thunderbird was updated with the correct settings:

| Setting        | Value           |
| -------------- | --------------- |
| Server         | 192.168.56.101  |
| Port           | 587             |
| Encryption     | STARTTLS        |
| Authentication | Normal Password |
| Username       | user1           |

After correcting the SMTP configuration, email transmission from Thunderbird worked correctly.

### 5.4 TLS Configuration Error in Postfix

#### Problem

Postfix failed to start properly and generated the following error in the mail logs:

```
fatal: bad boolean configuration: smtpd_use_tls = true
```

#### Cause

Postfix expects Boolean values such as **yes or no**, but the configuration mistakenly used **true**, which is not recognized.

#### Solution

The incorrect configuration was corrected in `/etc/postfix/main.cf`:

```bash
# Incorrect configuration:
smtpd_use_tls = true

# Correct configuration:
smtpd_use_tls = yes
```

Postfix was restarted after making the correction:

```bash
sudo systemctl restart postfix
```

The SMTP service then functioned normally.

### 5.5 SSL Certificate Error in Apache Web Server

#### Problem

When testing the Apache configuration, the following error occurred:
`SSLCertificateFile: file '/etc/pki/tls/certs/localhost.crt' does not exist`

#### Cause

The default SSL certificate referenced in the Apache configuration did not exist. A custom self-signed SSL certificate had been created earlier for the domain.

#### Solution

The Apache SSL configuration file was updated to reference the correct certificate:

```bash
SSLCertificateFile /etc/ssl/lhotse/web.lhotse.com.crt
SSLCertificateKeyFile /etc/ssl/lhotse/web.lhotse.com.key
```

The configuration was verified using:

```bash
sudo apachectl configtest
```

After confirming **Syntax OK**, Apache was restarted:

```bash
sudo systemctl restart httpd
```

The HTTPS website then functioned correctly.

### 5.6 FTP Login Failure (530 Permission Denied)

#### Problem

When connecting to the FTP server using FileZilla, the following error occurred:
`530 Permission denied`

#### Cause

The FTP server configuration did not allow local system users to log in.

#### Solution

The FTP configuration file `/etc/vsftpd/vsftpd.conf` was modified to enable local user access:

```
anonymous_enable=NO
local_enable=YES
write_enable=YES
```

The FTP service was then restarted:

```bash
sudo systemctl restart vsftpd
```

After applying these changes, users were able to log in and access their home directories through the FTP client.

### Conclusion

Throughout the configuration process, several issues related to **port conflicts, incorrect service configurations, SSL certificate references, and authentication settings** were encountered. These issues were systematically identified through log analysis, configuration file inspection, and testing. After applying the necessary corrections, all services including **DNS, Email, Web Server (HTTPS), and FTP** were successfully configured and tested using the Ubuntu client system.

