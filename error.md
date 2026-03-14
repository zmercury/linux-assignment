## Errors

### 1. 1st error encountered in named

Issue was the serial number on the forward and reverse lookup zone file
Serial was set to 0.
Fixed by adding a serial: 2026130310 instead of 0
![](./Linux-Images/Errors/19_Error_NamedError.png)
![](./Linux-Images/Errors/20_Error_NamedError_Status.png)
![](./Linux-Images/Errors/21_ErrorFixed_Named.png)

## 2. 2nd Error encountered when doing nslookup

Error was when nslookup was ran on server and client domain
it returned wrong ip
instead of 192.168.200.13 and 192.168.200.12
it returned 66.96.146.129

![](./Linux-Images/Errors/22_Error_NSLookup_Different_IP.png)

After doing some online research, we found out that:

- The DNS resolver reads `/etc/resolv.conf` from **top to bottom**
- It only tries the next nameserver if the first one doesn't respond.
  Our resolv.conf file
  ![](./Linux-Images/Errors/Pasted%20image%2020260313181948.png)
  Our resolv.conf file has some Adguard Nameserver. Since is reads in top-bottom approach, when we do nslookup it actually asks that nameserver instead of 192.168.200.12.
  Since lhotse.com is a real public domain. It returned the real domain ip.

Fix: just move our nameserver on the top so it grabs our private nameserver instead of adguard's nameserver
![](./Linux-Images/Errors/Pasted%20image%2020260313182403.png)
We restarted the bind `systemctl restart named` and tried the nslookup once again
![](./Linux-Images/Errors/Pasted%20image%2020260313182611.png)

## 3. 3rd Error encountered during configuring thunderbird on Ubuntu (Client)

![](./Linux-Images/Errors/16_ThunderBird_UserOne.png)
Error: couldn't add the user1 and user2 account on thunderbird
Cause: No MX DNS record for mail

Fixed by adding MX record on fwd.lhotse.com file
![](./Linux-Images/Errors/18_Error_Fixed_Email_MX_Record.png)

## 4. error encountered when starting vsftpd after configuration

![](./Linux-Images/Errors/Pasted%20image%2020260313212047.png)
Cause: typo in vsftpd config file, written passv instead of pasv
![](./Linux-Images/Errors/Pasted%20image%2020260313212254.png)
Fix: fixed the typo
![](./Linux-Images/Errors/Pasted%20image%2020260313212331.png)

## 5. 5th error encountered on same vsftpd

![](./Linux-Images/Errors/Pasted%20image%2020260313212559.png)
Cause: another typo on vsftpd config file, written allow_writable_chroot instead of allow_writeable_chroot
Fix:
![](./Linux-Images/Errors/Pasted%20image%2020260313212721.png)
