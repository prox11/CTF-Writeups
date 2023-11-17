- Név: Mr-Robot: 1
- Megjelenés Dátuma: 2016.06.28
- Szerző: Leon Johnson

Leírás: A gép a Mr Robot sorozat által inspirált VM. szintjét nagyjából egy kezdő nehézségre tenném, használt programok közé tartozik az NMAP,Gobuster/Dirbuster,WPScan. A következőkben ezt a folyamatot fogom dokumentálni. A cél, hogy megszerezzünk 3 darab flag-et.

Elsőnek is scanneljük végig a hálózatot, hogy beazonosítsuk a hostot. Ezt a netdiscover paranccsal tudjuk megtenni.


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/f22e3df3-fa89-4854-8cc3-fa3debcda729">
</p>


Most megnézzük milyen nyitott portokat találunk az ip címen (192.168.36.8). Ezt az Nmap parancs beírásával fogjuk elérni.

<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/9a4ecc75-31d5-42ea-aab3-9937375cc6c5">
</p>


Ahogy láthatjuk a 80-as port nyitva van, nézzük is meg, hogy mit találhatunk ezen a weboldalon.

<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/f22d21c3-5a0a-4e0e-8a91-43b3ef73065a">
</p>


Ha megnyitjuk a böngészőnket és elmegyünk az adott ip-re akkor egy rövid töltés után egy üzenetet kapunk majd egy terminált amiben a megadott parancsokat tudjuk beírni.

-	prepare: egy 1 percet videót kapunk
-	fsociety: szintén egy videó, viszont ez rövidebb
-	inform: pár képet kapunk, ami sorozatból néhány utalás
-	question: ismételten pár kép a sorozatból
-	wakeup: egy videórészlet a sorozatból
-	join: rövid szöveg után kér egy email címet, majd azzal az információval távozunk, hogy még értesítenek minket.

Ezekből a parancsokból, sok hasznos információt nem tudtunk meg, de nézzük meg, hogy milyen elérhető directoriek vannak még, amiket meg tudunk nézni. Ezt személy szerint a dirbuster(dirb) programmal fogom megnézni, de használható a gobuster is.  
A parancs a következő: **dirb http://ip-cím -r**

<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/204f18e2-8b71-4498-ae29-c6a8fd8996e2">
</p>


Sok érdekes dolgot találhatunk itt, minket itt a CODE:200 elemek érdekelnek elsősorban hiszen ezekre kaptunk OK választ a böngészőtől. Ezekből is a robots.txt ami nekünk hasznos lesz. Ha elnavigálunk ide, akkor találhatunk 1 filet és az első flaget is.

flag 1: 073403c8a58a1f80d943455fb30724b9

<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/321f251b-1098-4e70-ba7b-f3b63e0769ff">
</p>


Ha beírjuk és letöltjük az fsocity.dic-et akkor egy listát kapunk.  Ha tovább nézzük a mappákat néhány zsákutcát fogunk találni, viszont ami avatott szemeknek feltűnhet az az, hogy az oldal egy WordPress login paget is tartalmaz, ami utal, jelen esetben egy WordPress oldalra. Az alapvető admin-admin és társai kipróbálása után a WordPress beépített feature miatt tudtam, hogy nincsenek ilyen felhasználónevek.

<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/db19f191-cb28-4e28-9759-7682f8016e67">
</p>

Miután alaposabban átvizsgáltam a fsocity.dic file első pár sorát elkezdtem kipróbálni néhány kombinációt és hamarosan találtam is egy elliot nevű felhasználót. Ezután a wpscan programot használtam, ami a WordPress oldalak pluginjait tudja ellenőrizni, felhasználókat tud keresni, és bruteforce támadást is tud a login page ellen végrehajtani. 

<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/6eea0724-f023-496c-87e1-7a8f548e54d5">
</p>

Most egy bruteforce támadást hajtottam végre az elliot nevű felhasználó ellen az fsocity.dic pedig jelszóként használtam fel ebben a műveletben. Az eredmény pedig a következő lett:

**elliot – ER28-0652**

Beléptünk a WordPress admin panelbe.

<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/84e81687-a926-4b33-8933-0c52037137ae">
</p>

Innen tudunk egy reverse shellt feltölteni és így bejutni a gépbe, majd onnan eljutni a root jogig. Én ezt a pentestmonkey weboldalról letöltött shellt fogom használni, amiben átírom az ip-t és a portot.

https://pentestmonkey.net/

<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/9fe8d786-a5b5-4c10-a403-28c0f14f5117">
</p>

Ez után elindítunk egy listenert ami várni fogja, hogy felcsatlakozzunk rá.

<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/0418cea3-ab93-48fc-865e-5392bdd18421">
</p>


Ha ez is megvan, akkor elnavigálunk a következő címre: http://ip-cím/wp-content/twentyfifteen/shellnev.php és ha mindent jól csináltunk a következőt kell, hogy kapjuk.


<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/2b88fcbd-cf9b-4a9d-a039-8d28234dd3c5">
</p>

Bent is volnánk. Ám közel se vagyunk még kész. Most jön, hogy teljes mértékben megszerezzük a gépet a root jog elérésével. Kezdésnek nézzünk szét a gépen az alapvető mappákban, ahol lehetnek érdekes dolgok, amit fel tudunk használni. Nem is kell sokat keresgélni, ugyanis /home/robot mappában megtaláljuk a következő flagat és egy érdekes filet.

<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/5ead9cd3-122c-4d57-bd97-8e76437e5703">
</p>

Egy file a robot nevű felhasználó jelszó hashéhez, ami a név alapján egy raw md5 hash lesz. Több lehetőség is van ennek a hashnek a megfejtésére, remek program például a john the reaper vagy az online crackstation is.

<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/647e8539-f40c-4e5e-b44e-72e4c45eba2c">
</p>

<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/b459732b-903c-4f12-b091-384e165752b1">
</p>

Ezekből kiderül, hogy a robot userhez a jelszó nem más mint: abcdefghijklmnopqrstuvwxyz
és a következő flag is megvan:

flag 2: 822c73956184f694993bede3eb39f959

Már csak 1 flag van hátra és egy root jog, hogy kimaxoljuk ezt a feladatot. Most, hogy a robot userbe vagyunk, nézzünk szét is  egy kicsit, hogy milyen jogosultságaink vannak. A sudo –l parancsra nem dob ki semmit és a mappánkban sincs már semmi használható. Egy kis ügyeskedés után találhatunk érdekes dolgot a gépen a következő kóddal:

**find / -perm -u=s -type f 2>/dev/null**

Ez a parancs kilistázza az összes SUID filet a gépen, amivel ha találunk nem jól beállított filet egyszerűen végezhetünk egy privilege escalationt. 

<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/1f0e8ee6-1408-436e-aaf4-dae9367f0b6b">
</p>

Ezekből sokat nem tudunk felhasználni általában, sőt van olyan is, hogy egyáltalán nem tudjuk őket felhasználni. Viszont jelenleg nem ez a helyzet. Van 1 ami kitűnik. /usr/local/bin/nmap. A https://gtfobins.github.io/ oldalon több száz ilyen nem jól konfigurált példa van. Ilyen itt az nmap is.

<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/9fcead55-309e-4111-af94-baf3e5197125">
</p>

Az innen felhasznált pár sorral pedig könnyen megszerezhetjük a kívánt eredményt.

<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/07dede0c-0694-49eb-a5ca-9b47c6568d17">
</p>


Innentől kezdve megszereztük a gépet és begyűjthetjük a harmadik flagat is, ezzel együtt a VM-et.

<p align="center">
  <img src="https://github.com/prox11/CTF-Writeups/assets/148764185/16d2cabe-d1b1-4afc-8732-5d8c8bb40fff">
</p>


flag 3: 04787ddef27c3dee1ee161b21670b4e4



