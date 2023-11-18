- Név: Matrix: 1
- Megjelenés dátuma: 2018.08.19
- Szerző: Ajay Verma

**Leírás:** Egy egyszerű CTF gép, amiben a root jogig kell eljutnunk. Sok easter egg és félrevezetés is megtalálható a gépben, ami nem ad nekünk plusz információt. A gépet egy kezdő/haladó szintre sorolnám.

Elsőnek is végigscannelem a hálózatot a netdiscover paranccsal és beazonosítom a célgépet.


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/4647c772-ccea-4f86-b66a-ee5a7aba449c">
</p>

Következőnek megnézem a nyitott portokat és néhány vizsgálatot is elvégzek az nmap-el

**sudo nmap 192.168.10.73 -sV -sC –O**


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/9d5e04fd-e28b-4160-b71b-1a8d292271dd">
</p>


A report az SSH(22) és két webszervert dob ki(80,31337). Ha ezeket megvizsgáljuk a következő 2 oldal fogad minket. 


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/7aee34a2-6db7-43ba-9f72-00e1bb8efd57">
</p>


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/177d714d-4a56-4936-aa5c-f80418366c8f">
</p>


Ha megnézzük a 80-as porton található weboldal forráskódját akkor nem találunk semmi érdekeset. Viszont ha a 31337-es porton találhatóét vizsgáljuk meg akkor találhatunk valami érdekeset. 


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/6ad0835f-40a5-415b-9008-57c33a680607">
</p>


Egy base64 kódolású szöveg. Ezt egy egyszerű sorral tudjuk dekódolni. 

**echo 'ZWNobyAiVGhlbiB5b3UnbGwgc2VlLCB0aGF0IGl0IGlzIG5vdCB0aGUgc3Bvb24gdGhhdCBiZW5kcywgaXQgaXMgb25seSB5b3Vyc2VsZi4gIiA+IEN5cGhlci5tYXRyaXg=' | base64 –d**


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/347a0ed3-a9e2-43e1-b41d-3884d89a078b">
</p>


Eredménynek a következőt dobta ki: 
"Then you'll see, that it is not the spoon that bends, it is only yourself. " > Cypher.matrix

Ha ellátogatunk ezen a porton erre a címre, akkor le fog tölteni egy filet. A file tartalma egy brainfuck programkód. Az interneten találhatunk is erre egy decodert.


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/77f5fe39-ffea-482d-aa4b-f938fbc7ec07">
</p>


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/dcd130bb-b172-4b7d-924d-dca74a063d2a">
</p>


A weboldal a következő: https://www.dcode.fr/brainfuck-language

A következő eredményt dobta ki: 

"You can enter into matrix as guest, with password k1ll0rXX
Note: Actually, I forget last two characters so I have replaced with XX try your luck and find correct string of password."

Generáljunk egy wordlistet a crunch programmal, amiben az utolsó két X helyét kiegészítjük.

**crunch 8 8 -t k1ll0r%@ -o matrixdic.txt**


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/fba044ad-df39-402b-aa55-9f22e898f995">
</p>


(https://www.kali.org/tools/crunch/)

Ezután pedig hydrával bruteforceoljuk az ssh portot.

**hydra -l guest -P matrixdic.txt ssh://192.168.10.73**

<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/f4a98a62-a9e2-49b7-9142-9e19bb01f755">
</p>


a jelsző: k1ll0r7n

Ha bejelentkezünk az ssh-n keresztül a következő fogad.

<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/b4a51c0a-9175-444e-9455-4976c585e54f">
</p>


Egy restricted shell. Sok trükk van erre, ami található az interneten, hogy kicselezzük ezt is. Az echo paranccsal például tudjuk helyettesíteni az ls parancsot.
pl: 
- echo /*
- echo /home/guest/*

Találunk is egy /home/guest/prog/vi filet. Ha simán beírjuk a terminálba, hogy vi majd a következőt beírjuk kaphatunk egy használható shellt. 

**:!/bin/bash**


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/c6db10a3-90a0-47a3-8fb1-147169854e4f">
</p>

export PATH=/usr/bin:/bin


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/fb47cc93-525d-4934-b65d-4b43e6723396">
</p>


Majd egyszerűen a sudo –l paranccsal megnézzük, hogy milyen lehetőségeink vannak. Ez után látszódik, hogy bármit futtathatunk root userként. A sudo su paranccsal át is lépünk a root felhasználóba és kiíratjuk a flag.txt filet
 


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/05aab510-8f07-454e-b7b3-0730736757bb">
</p>

