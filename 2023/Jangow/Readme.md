- Név: Jangow: 1.0.1
- Megjelenés dátuma: 2021.11.04
- Szerző: Jangow

Leírás: A gép a vulnhubról származó VM, aminek szintén több megoldása is létezik és a nehézségét is a könnyű szintre sorolnám. Több jól ismert eszközt is fogunk használni. A célunk a gépen a root jogosultság megszerzése.

Kezdésnek a netdiscover parancsot fogom használni, amivel beazonosítom a célgépet.
Jangow, Vulnhub



<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/fe9e04bd-7b45-420f-89ea-d51ad7e888ad">
</p>

Miután ez megvan elvégezzük az alapvető scannelést az nmap-el amire a következő eredményt kapjuk.


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/e3b2467a-1486-4b22-9f96-c6bc766e469c">
</p>


A nyitott portok között megtaláljuk a jól ismert 21 és 80-as portot. Az nmap általában jelzi, ha az ftp porton engedélyezett a vendég belépés, de próbáljuk meg, hátha. Ezt az ftp ip-cím paranccsal tudjuk megtenni és utána a bejelentkező névnek és a jelszónak is a következőt kell megadni: **anonymous**

<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/4eb6123e-b1d7-4d72-b182-bb9f803cb551">
</p>


Ezután nézzük meg a 80-as portot a böngészőnkben. A következő fogad minket:

<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/65ce47f3-84d1-4686-a8ab-ae1791760441">
</p>


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/22df101e-2ad3-4889-a871-2811d7750dd8">
</p>


Ezt követően szétnézhetünk a honlapon is és én még szét fogok nézni a gobusterrel is, hogy találok-e valami érdekes directoryt. Két kiindulópontot is észrevehetünk, az egyik, hogy az oldalon egy Wordpress oldalt fut.

<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/81002365-80c3-4989-baa3-edcf7004a7e3">
</p>


A másik érdekesség, hogy ha a weboldalon megnézzük az egyes linkeket találhatunk egy furcsaságot, ami nagy sérülékenységre utalhat.

<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/abec05e0-eb7f-476e-a3b1-6dfeb6388120">
</p>


Ezen a gépen a példát láthatunk a command execution vulnerabilityre, ahol a saját kódunkat tudjuk lefuttatni, a helytelenül konfigurált weboldal miatt. Ezt az egyszerű ls paranccsal le is tudjuk tesztelni.

<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/53cc8a74-e643-4684-bc0d-7881518f662e">
</p>


Látható is, hogy kilistázza a jelenlegi könyvtárban lévő mappákat. Innen több módszerrel is be lehet jutni a gépbe, ezek közül fogok bemutatni egyet.
http://192.168.10.71/site/busque.php?buscar=cat /var/www/html/.backup sorral megnézhetünk egy backup filet, amiben találunk egy felhasználónév jelszó párost.


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/7f4c60b9-8d41-46dc-98f2-035d73a19eab">
</p>


Ennek segítségével beléphetünk az ftp szerverre.

- Felh: jangow01

- Jelszó: abygurl69


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/e4def77b-e1c3-4e28-9ad7-b5fbc9e5bd3a">
</p>


Ha elnavigálunk a /home/jangow01 mappába, akkor észrevehető, hogy hozhatunk létre filekot is ott. Ezt követően belépünk a jangow01 felhasználóba a másik VM-en és ott keresünk további sérülékenységeket.


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/6b355899-8711-4901-b5b4-bb3530756b96">
</p>


A uname –a paranccsal meghatározhatjuk, hogy az operációsrendszer linux 4.4.0-31 generic. Nem is kellett sokat keresni, hogy megtaláljam az exploitot, ami pontosabban a  **CVE:2017-16995** 

link: https://www.exploit-db.com/exploits/45010

Ezután létrehozunk egy filet .c kiterjesztéssel és feltöltjük az ftp szerveren keresztül a jangow01 felhasználó mappájába. 


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/9ae46c14-9085-4e4a-9dab-2e0ee53a29af">
</p>

Miután ez megvolt a következőket hajtottam végre.

- gcc exploit.c –o exploit
- chmod +x exploit
- ./exploit

Végül lefutattjuk a scriptet, amit létrehoztunk és egy whoami kóddal ki is derült, hogy sikeres volt a privilege escalation és eljutottunk a root jogig.


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/60ff0d08-08b0-4987-af93-3c6f96b1a04f">
</p>

<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/c8fb5481-5da7-4f44-b391-043fd8c0a508">
</p>





