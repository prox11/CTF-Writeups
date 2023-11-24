- Name: Scriptkiddie 
- Release Date: 2021.07.20 
- Author: 0815R2d2

Description: This is a very simple machine that effectively showcases the functioning of the Metasploit framework. The entire process to gain root access takes approximately 5-10 minutes.

First, let's identify the machine using the 'netdiscover' command


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/8a24b762-d3f0-474d-a45c-e88a5c629dae">
</p>

Following this, use the 'nmap' command to check for open ports:

**sudo nmap 192.168.10.74 -sV -sC –O**


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/5f331a8e-2d62-4591-9bef-d42156f1821f">
</p>


Several ports are open. I checked several of these but couldn't find anything useful. The website is a Wordpress site, but I couldn't identify any vulnerabilities there.


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/4ee34838-8f9f-4dde-bde2-fad4635e401d">
</p>


**smbmap -H 192.168.10.74**


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/d12c63ef-056b-41c5-9300-e3acf2d76d3b">
</p>


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/8b462dee-b7dc-4179-a467-182be322a896">
</p>

The key lies in the FTP (21) port. A quick internet search reveals that it is vulnerable and allows Remote Code Execution. All we need to do is open Metasploit.

**msfconsole**


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/c06f3228-0e30-4769-b19a-0afb3a299e48">
</p>


**search proftpd 1.3.3c**


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/764e5e1b-6224-44eb-a9d3-f9f043bd840f">
</p>


Next, using the 'use {exploit}' command, we can select it. Upon typing 'options', it displays what needs to be configured. With the 'set {option name} {data}' command, we can configure these settings. Then, using 'show payloads', we select a payload which we can set with 'set payload {payload}'.

https://docs.metasploit.com/docs/pentesting/metasploit-guide-setting-module-options.html


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/a24221ff-be8b-4917-b8bc-40c617bb3157">
</p>


Finally, the 'run/exploit' command executes the script. If everything is configured correctly, we successfully gain access to the system with immediate root privileges.


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/898692f4-0b4f-461e-b8b8-3626655aba86">
</p>



<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/48f4b2b0-9cda-4481-b670-4b11599356e8">
</p>
