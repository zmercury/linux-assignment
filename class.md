# DNS Server Configuration in Linux

---

Prerequisites before DNS configuration:

1. IP Address of System : (192.168.200.10)
2. Hostname : (server.lhotse.com)
3. New Domain name : (lhotse.com)
4. DNS Server IP Address : (192.168.200.10)
5. ServerOS : CentOS or Rocky

## Configuration

1. Verification
   a. Package Verification
   - DNS Package installed or not?
     `rpm -q bind`
   - if, package name is visible then its installed

2. Configuration

Step 1: Install DNS Packages

```bash
dnf -y install bind bind-utils
```

Step 2: Edit name configuration file

```bash
cd /etc
ls | grep named
# named.conf is what we're searching
```

i. make a backup of the named.conf
`cp named.conf named.conf.backup`

ii. Go to line number 11 and add any instead of whatever ip there is

iii. Go to line number 19 and do the same

```conf
zone "lthose.com" IN {
  type master;
  file "fwd.lthose.com";
  allow-update {none;};
};

zone "200.168.192.in-addr.arpa" IN {
  type master;
  file "rev.lthose.com";
  allow-update {none;};
};
```

Step 3: Enable firewall for DNS

```bash
firewall-cmd --permanent --add-service=dns
firewall-cmd --permanent --add-port=53/tcp
firewall-cmd --permanent --add-port=53/udp
firewall-cmd --reload #reload the firewall to apply changes
```

Step 4: Configure forward and reverse lookup zone

a. Create zone file

```bash
cd /var/named

# create two files (fwd and rsv)
# add configurations
```

b. edit and configure both files as per scenario

Step 5: Enable and Start named

```bash
systemctl enable named
systemctl start named
systemctl status named
```

Step 6: Verify DNS

```bash
nslookup 192.168.200.10
nslookup lhotse.com
```
