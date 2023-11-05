# Sniff-n-Scan

Juho Tuovinen

Tässä raportissa aioin kertoa kuinka suoritin tehtävät, jotka on annettu Haaga Helian Tunkeutumisteustaus kurssilla. Tehtävät löytyvät [täältä](https://terokarvinen.com/2023/eettinen-hakkerointi-2023/#h2-sniff-n-scan).

Tietokoneen speksit, jolla suoritan toimenpiteen:
- Apple MacBook 2015
- macOs Monterey versio 12.7
- Ram -muistia 16GB
- levytilaa/muistia vapaana 49,11 GB.

Ohjelmistot
- UTM Version 4.1.6 (75)
- VM OS: Kali Linux 2023.3 (kali-rolling)

## Lue/katso ja tiivistä.

Hoikkala "joohoi" 2020: [Still Fuzzing Faster (U fool)](https://www.youtube.com/watch?v=mbmsT3AhwWU). In HelSec Virtual meetup #1.

- Esityksessä Joona Hoikkala esittelee ohjelmistoaan ffuf:ia ja kertoo Web Fuzzauksesta.
- käyttäjä voi suodattaa pyyntöjä suodattimien avulla
- tavallisimmat kohteet ovat GET-parametrit, Headerit ja POST data
- Matchereiden avulla voidaan etsiä yhtenäisyyksiä, esim. vastauskoodit, sisältö tai vastauksien koko
- Vastaavia työkaluja: Burp Suite, OWASP ZAP, dirb, dirbuster, wfuzz, gobuster
- Joona esittelee ffuf:ia. Hän suosittelee ffufia käyttäessä sanalistojen hyödyntämisen pyyntöjä tehdessä. Hän asittelee muutaman yleisimmän parametrin merkitysen:
   * <code>-w</code> määrittää sanalistan jota halutaan hyödyntää
   * <code>-u</code> URL, johon pyynnöt/hyökkäys tehdään
   * <code>-e</code> laajettu haku, johon voilisätä esim. <code>.php</code>, jolloin ohjelma etsii polkuja ja tiedostoja, jotka päättyvät .php
   * <code>-c</code> väritetty tulostus
   * <code>-v</code> verbose; saadaan yksityiskohtaisempaa tietoa pyynnön edistymisestä
   * <code>FUZZ</code>kohta, johon fuzzataan hyödyntäen sanalistoja
- ffufia voi hyödyntää myös käyttäjätunnuksien murtamisessa bruteforce tekniikalla
- ffufia voihyödyntää myös XSS-injektioiden kehittämisessä

Lyon 2009: Nmap Network Scanning: Chapter 15. Nmap Reference Guide:
- [Port Scanning Basics](https://nmap.org/book/man-port-scanning-basics.html)
  * <code>Open</code>: Portti on avoin ja palvelu on käynnissä kyseisellä portilla.
  * <code>Closed</code>: Portti on suljettu eikä palvelua ei ole käynnissä kyseisellä portilla.
  * <code>Filtered</code>: Portti on suodatettu, eli Nmap ei saanut vastausta porttiin liittyviin kyselyihin, eikä pysty sanomaan onko portti auki. 
  * <code>Unfiltered</code>: Portti voi olla auki tai suljettu, mutta Nmap ei pysty sitä täsmällisesti määrittämään.
  * <code>Open|Filtered</code>: Nmap ei pysty varmuudella sanomaan, onko portti avoin vai suodatettu. Portti ei vastaa täysin avoimesti eikä täysin suodatetusti.
  * <code>Closed|Filtered</code>: Nmap ei pysty varmuudella sanomaan, onko portti suljettu vai suodatettu. Portti ei vastaa täysin suljetusti eikä täysin suodatetusti.
- [Port Scanning Techniques](https://nmap.org/book/man-port-scanning-techniques.html)
  * <code>-sS (TCP SYN scan)</code>: SYN-skannaus mahdollistaa nopean ja huomaamattoman porttien tarkistuksen. Tätä tekniikkaa kutsutaan puoliksi avoimeksi skannaukseksi, ja se perustuu SYN-pakettien lähettämiseen odottaen vastausta, jonka perusteella voidaan erottaa avoimet, suljetut ja suodatetut portit.
  * <code>-sT (TCP connect scan)</code>: TCP Connect -skannaus on Nmapin oletusmenetelmä, kun SYN-skannaus ei ole mahdollinen. Se pyytää käyttöjärjestelmää luomaan yhteyden kohdekoneeseen ja porttiin, mikä tekee siitä vähemmän huomaamattoman, mutta hitaamman vaihtoehdon verrattuna SYN-skannaukseen.
  * <code>-sU (UDP scans)</code>: UDP-skannus on hidas ja haastava skannausmenetelmä. Se lähettää UDP-paketin jokaiseen kohdeporttiin ja tulkkaa vastauksia ICMP-virheviesteistä, kuten porttiin tavoittamattomista virheistä, jotta voidaan määrittää porttien tilat.

## a) Fuff. Ratkaise [Teron ffuf-haastebinääri](https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/?fromSearch=ffuf#your-turn---challenge). Artikkelista [Find Hidden Web Directories - Fuzz URLs with ffuf](https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/)voi olla apua.

Ffuf-työkalu ei ole minulle aikaisemmin tuttu, joten luin artikkelin ja lähdin etenemään harjoitusta artikkelin mukaan. Asensin virtuaalikoneelleni harjoituskohteen artikkelista löytyvien ohjeiden mukaan:

````
$ wget https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/dirfuzt-0
$ chmod u+x dirfuzt-0
$ ./dirfuzt-0
Learn more at TeroKarvinen.com
http://127.0.0.2:8000

````

Avasin osoitteen <code> http://127.0.0.2:8000 </code> virtuaalikoneeni selaimessa ja se näytti samalta kuin ohjeessa, eli asennus on onnistunut.

<img src="/images/nothing.png" alt="" title="" width="70%" height="70%">

Seuraavaksi asensin ffuf-työkalun artikkelin ohjeiden mukaan:

````
$ wget https://github.com/ffuf/ffuf/releases/download/v2.0.0/ffuf_2.0.0_linux_amd64.tar.gz
$ tar -xf ffuf_2.0.0_linux_amd64.tar.gz
$ ./ffuf
Fuzz Faster U Fool - v2.0.0
...

````

Lastasin Daniel Miesslerin [Seclists](https://github.com/danielmiessler/SecLists) sanalistan.

    $ wget https://raw.githubusercontent.com/danielmiessler/SecLists/master/Discovery/Web-Content/common.txt


Ohjeissa kehotetaan varmuudeksi ottamaan kone pois internetistä, joten teen sen.

Seuraavaksi syötin komennon <code> ./ffuf -w common.txt -u http://127.0.0.2:8000/FUZZ </code> ja tuloste oli seuraava:

````

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.0.0
________________________________________________

 :: Method           : GET
 :: URL              : http://127.0.0.2:8000/FUZZ
 :: Wordlist         : FUZZ: /home/kali/common.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
________________________________________________

[Status: 200, Size: 132, Words: 6, Lines: 10, Duration: 0ms]
    * FUZZ: .bash_history

[Status: 200, Size: 132, Words: 6, Lines: 10, Duration: 0ms]
    * FUZZ: .bashrc

.... tuloste jatkuu tuhansia rivejä ....
````


TEHTÄVÄ ALKAA:


Latasin harjoituskohteen [dirfuzt-1](https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/dirfuzt-1) ja ajoin sen. 

````
wget https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/dirfuzt-1
chmod u+x dirfuzt-1
./dirfuzt-1
````
<img src="/images/nothing.png" alt="" title="" width="70%" height="70%">

Ennen Ffuf:n käyttöä kytken virtuaalikoneen pois verkosta. Kokeilin manuaalisesti löytyisikö sivulta <code>/admin</code>, <code>/secret</code> tai esim <code>/robots.txt</code>, mutta ei löytynyt. Kokeilin ensiksi komentoa <code>./ffuf -w common.txt -u http://127.0.0.2:8000/FUZZ</code>, jossa käytän aikaisemmin ladattua sanalistaa.


````

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.0.0
________________________________________________

 :: Method           : GET
 :: URL              : http://127.0.0.2:8000/FUZZ
 :: Wordlist         : FUZZ: /home/kali/common.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
________________________________________________

[Status: 200, Size: 154, Words: 9, Lines: 10, Duration: 0ms]
    * FUZZ: .bash_history

[Status: 200, Size: 154, Words: 9, Lines: 10, Duration: 0ms]
    * FUZZ: .bashrc


.... tuloste jatkuu tuhansia rivejä...
````

Tulosteesta nähdään, että jokaisen ei-toivotun vastauksen koko on 154 tavua eli 154 ASCII-merkkiä. Filteröin käyttäen apuna tätä tietoa komennolla <code>./ffuf -w common.txt -u http://127.0.0.2:8000/FUZZ -fs 154</code>. Tulosteesta löytyi seuraavat tiedot:

````
[Status: 301, Size: 41, Words: 3, Lines: 3, Duration: 0ms]
    * FUZZ: .git

[Status: 200, Size: 178, Words: 6, Lines: 11, Duration: 2ms]
    * FUZZ: .git/HEAD

[Status: 200, Size: 178, Words: 6, Lines: 11, Duration: 3ms]
    * FUZZ: .git/index

[Status: 200, Size: 178, Words: 6, Lines: 11, Duration: 3ms]
    * FUZZ: .git/config

[Status: 200, Size: 178, Words: 6, Lines: 11, Duration: 0ms]
    * FUZZ: .git/logs/

[Status: 301, Size: 64, Words: 3, Lines: 3, Duration: 0ms]
    * FUZZ: render/https://www.google.com

[Status: 200, Size: 182, Words: 6, Lines: 11, Duration: 0ms]
    * FUZZ: wp-admin
````
<code>/wp-admin</code>-kansio kuullosti kiinnostavimmalta, joten liitin sen URL:iin <code>http://127.0.0.2:8000/wp-admin</code>. TADAA! Oikeasivu ja lippu löytyi!
<img src="/images/flag.png" alt="" title="" width="70%" height="70%">

Lippu: FLAG{tero-wpadmin-3364c855a2ac87341fc7bcbda955b580}

## b) Fuffme. Asenna [Ffufme](https://terokarvinen.com/2023/fuffme-web-fuzzing-target-debian/) harjoitusmaali paikallisesti omalle koneellesi. Ratkaise tehtävät (kaikki paitsi ei "Content Discovery - Pipes")

Asensin Fuff:in ja Fuffme:n artikkelin ohjeiden mukaan. Otin koneeni pois netistä ja aloitin testaamisen. 

### Basic Content Discovery
Komennolla <code>ffuf -w $HOME/wordlists/common.txt -u http://localhost/cd/basic/FUZZ</code> löysin halutut kansiot <code>class</code> ja <code>development.log</code>.

`````

$ ffuf -w $HOME/wordlists/common.txt -u http://localhost/cd/basic/FUZZ

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://localhost/cd/basic/FUZZ
 :: Wordlist         : FUZZ: /home/kali/wordlists/common.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

class                   [Status: 200, Size: 19, Words: 4, Lines: 1, Duration: 20ms]
development.log         [Status: 200, Size: 19, Words: 4, Lines: 1, Duration: 14ms]
:: Progress: [4686/4686] :: Job [1/1] :: 2777 req/sec :: Duration: [0:00:02] :: Errors: 0 ::


`````

### Content Discovery With Recursion

Tässä tehtävässä käytetään recursion-kytkintä. Kytkin kertoo ohjelmalle, että jos se kohtaa hakemiston, sen tulee aloittaa uusi skannaus hakemistossa ja lopettaa kunnes hakemistoja ei enää löydy. Suoritin komennon <code>ffuf -w ~/wordlists/common.txt -recursion -u http://localhost/cd/recursion/FUZZ/</code>, joka käyttää kytkintä. Tuloste näyttää seuraavaa:

````
admin                   [Status: 301, Size: 0, Words: 1, Lines: 1, Duration: 16ms]
[INFO] Adding a new job to the queue: http://localhost/cd/recursion/admin/FUZZ

[INFO] Starting queued job on target: http://localhost/cd/recursion/admin/FUZZ

users                   [Status: 301, Size: 0, Words: 1, Lines: 1, Duration: 6ms]
[INFO] Adding a new job to the queue: http://localhost/cd/recursion/admin/users/FUZZ

[INFO] Starting queued job on target: http://localhost/cd/recursion/admin/users/FUZZ

96                      [Status: 200, Size: 19, Words: 4, Lines: 1, Duration: 16ms]
:: Progress: [4686/4686] :: Job [3/3] :: 2702 req/sec :: Duration: [0:00:01] :: Errors: 0
````

Hakemistot <code>/admin</code>, <code>/admin/users</code> ja <code>/admin/users/96</code> pitäisi löytyä ja niinhän ne löytyivätkin.

### Content Discovery With File Extensions

Tehtävän suomennos: "Olemme törmänneet hakemistoon nimeltä /logs, mutta sisältöä ei nähdä. Voimme tehdä oletuksen, että tässä hakemistossa olevat tiedostot käyttävät .log-tiedostopäätettä. Käytä skannausta -e-kytkimellä määrittääksesi tiedostopääte, joka lisätään jokaisen sanalistan sanan loppuun oikean lokitiedoston löytämiseksi." Lähdin kokeilemaan annetulla komennolla <code>ffuf -w ~/wordlists/common.txt -e .log -u http://localhost/cd/ext/logs/FUZZ/</code>. Skannauksen jälkeen hakemisto <code>/logusers.log</code> pitäisi löytyä.

Tuloste:

````
.......more.....
:: Progress: [8226/9372] :: Job [1/1] :: 2222 req/sec :: Duration: [0:00:03] :: Errors: 0
:: Progress: [8519/9372] :: Job [1/1] :: 2564 req/sec :: Duration: [0:00:03] :: Errors: 0
users.log               [Status: 200, Size: 19, Words: 4, Lines: 1, Duration: 15ms]
:: Progress: [8664/9372] :: Job [1/1] :: 2020 req/sec :: Duration: [0:00:03] :: Errors: 0
:: Progress: [8772/9372] :: Job [1/1] :: 1960 req/sec :: Duration: [0:00:03] :: Errors: 0
......more.....
````
Hakemisto löyty!

### No 404 Status

Tehtävän suomennos: "Ideaalimaailmassa kaikki verkkosivustot vastaisivat oikein oikeilla HTTP-tilakoodilla. Kokeillaan ajaa seuraava ffuf-esimerkki ja katsotaan, mitä tapahtuu: <code>ffuf -w ~/wordlists/common.txt -u http://localhost/cd/no404/FUZZ</code>."

Tuloste on pitkä ja siitä näkyy, että jokainen pyytämäsi tiedosto on palannut löydettynä. Se johtuu siitä, siitä, että verkkosivusto, joka näyttää "Sivua ei löydy" -viestin, ei palauta 404-otsikkotietoa.

"Huomaat, että "Sivua ei löydy" -sivulla on johdonmukaisesti tiedostokoko 669 tavua. Suoritetaan ffuf-komento uudelleen, mutta tällä kertaa käytämme -fs-kytkintä, joka suodattaa pois kaikki tulokset, joiden pituus on 669 tavua: ffuf -w ~/wordlists/common.txt -u http://localhost/cd/no404/FUZZ -fs 669"

Syötin komennon ja sain seuraavan tulosteen:

````
:: Progress: [3516/4686] :: Job [1/1] :: 2061 req/sec :: Duration: [0:00:01] :: Errors: 0
secret                  [Status: 200, Size: 25, Words: 4, Lines: 1, Duration: 10ms]
:: Progress: [3689/4686] :: Job [1/1] :: 1941 req/sec :: Duration: [0:00:01] :: Errors: 0
````
Ohjeen mukaan <code>secret</code>-hakemisto pitäisi löytyä ja niin löytyikin.

### Param Mining

Tehtävän suomennos: "Kun tarkastelet sivua /cd/param/data, näet viestin "Required Parameter Missing", ja sivulle on asetettu HTTP-tilakoodi 400, mikä tarkoittaa Bad Request -virhettä. Käyttämällä seuraavaa pyyntöä, voimme yrittää löytää puuttuvan parametrin: ffuf -w ~/wordlists/parameters.txt -u http://localhost/cd/param/data?FUZZ=1"

Tuloste:

````
debug                   [Status: 200, Size: 24, Words: 3, Lines: 1, Duration: 11ms]
:: Progress: [2450/2588] :: Job [1/1] :: 2702 req/sec :: Duration: [0:00:01] :: Errors: 0
:: Progress: [2588/2588] :: Job [1/1] :: 2150 req/sec :: Duration: [0:00:01] :: Errors: 0
:: Progress: [2588/2588] :: Job [1/1] :: 2150 req/sec :: Duration: [0:00:01] :: Errors: 0
````

Ohjeen mukaan puuttuva parametri <code>debug</code> pitäisi löytyä ja niin löytyikin.

### Rate Limited

Tehtävän suomennos: "Ajoittain palvelut rajoittavat pyyntöjen määrää sekunnissa. Tämä tarkoittaa, että voit lähettää vain tietyn määrän pyyntöjä sekunnissa. Tässä tapauksessa hakemisto, jota yritämme fuzzata, on rajoitettu 50 pyyntöön sekunnissa. Jos yrität ajaa seuraavan komennon, huomaat, että saat paljon 429 HTTP-tilakoodeja, mikä tarkoittaa, että olet tilapäisesti estetty lähettämästä pyyntöjä muutaman sekunnin ajan. Käytämme -mc-kytkintä näyttääksemme vain HTTP-tilakoodit 200 ja 429. Tämä auttaa meitä seuraamaan sekä onnistuneet että rajoitukset aiheuttaneet pyynnöt.

Ajoin ohjeessa annetun komennon <code>ffuf -w ~/wordlists/common.txt -u http://ffuf.test/cd/rate/FUZZ -mc 200,429</code> ja sain seuraavan syötteen:

````
:: Progress: [636/4686] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: 597 :
:: Progress: [2199/4686] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: 2159
:: Progress: [3544/4686] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: 3504
:: Progress: [4686/4686] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: 4686
:: Progress: [4686/4686] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: 4686
````
Tästä nähdään, että virheitä on paljon ja vastaukset eivät ole tulleet odotetussa ajassa.

"Kokeile nyt ajaa seuraava komento. -p-kytkin aiheuttaa sovelluksen odottavan 0,1 sekuntia jokaista pyyntöä kohti ja -t-kytkin luo 5 versiota ffuf-työkalusta, mikä tarkoittaa enintään 50 pyyntöä sekunnissa."

    
    ffuf -w ~/wordlists/common.txt -t 5 -p 0.1 -u http://ffuf.test/cd/rate/FUZZ -mc 200,429


Sain tulosteen:

`````
....MORE....
:: Progress: [4680/4686] :: Job [1/1] :: 56 req/sec :: Duration: [0:01:35] :: Errors: 468
:: Progress: [4685/4686] :: Job [1/1] :: 56 req/sec :: Duration: [0:01:35] :: Errors: 468
:: Progress: [4686/4686] :: Job [1/1] :: 56 req/sec :: Duration: [0:01:35] :: Errors: 468
:: Progress: [4686/4686] :: Job [1/1] :: 51 req/sec :: Duration: [0:01:35] :: Errors: 468
`````

En saanut toivottua tulosta. Tiedostoa <code>oracle</code> ei löytynyt. Vian selvitys kesken.

### Subdomains - Virtual Host Enumeration

Tehtävän suomennos: "FFUF:ia voidaan käyttää aliverkkotunnusten löytämiseen virtuaalisten isäntien ja Host-otsikon muuttamisen avulla. Kokeile ajaa seuraava FFUF-komento: <code>ffuf -w ~/wordlists/subdomains.txt -H "Host: FUZZ.ffuf.me" -u http://localhost</code>"

Tuloste:

`````
....MORE....
:: Progress: [1907/1907] :: Job [1/1] :: 1459 req/sec :: Duration: [0:00:01] :: Errors: 0
:: Progress: [1907/1907] :: Job [1/1] :: 1459 req/sec :: Duration: [0:00:01] :: Errors: 0
:: Progress: [1907/1907] :: Job [1/1] :: 1459 req/sec :: Duration: [0:00:01] :: Errors: 0

`````
"Kun tarkastelet tuloksia, huomaat, että jokainen tulos palautuu koon ollessa 1495 tavua. Kokeile nyt ajaa alla oleva FFUF-skannaus, mutta tällä kertaa käytä -fs-kytkintä suodattaaksesi pois kaikki tulokset, joiden koko on 1495 tavua: <code>ffuf -w ~/wordlists/subdomains.txt -H "Host: FUZZ.ffuf.me" -u http://localhost -fs 1495</code>"

Tuloste:

`````
redhat                  [Status: 200, Size: 15, Words: 2, Lines: 1, Duration: 3ms]
:: Progress: [1907/1907] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: 0 ::
`````

Tehtävän mukaan subdomain <code>redhat</code> pitäisi löytyä ja niinhän se löytyi!

## Porttiskannaa paikallinen kone (127.0.0.2 tms), sieppaa liikenne snifferillä, analysoi.
Ensiksi varmistin, että koneeni on kytketty pois internetistä vahinkojen välttämiseksi.
### nmap TCP connect scan -sT

Annoin komennon <code>nmap -sT localhost</code> ja käynnistin Wiresharkin analysoidakseni tuloksia.

Nmap:

````
Starting Nmap 7.94 ( https://nmap.org ) at 2023-11-04 13:10 EET
mass_dns: warning: Unable to determine any DNS servers. Reverse DNS is disabled. Try using --system-dns or specify valid servers with --dns-servers
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00018s latency).
Other addresses for localhost (not scanned): ::1
Not shown: 999 closed tcp ports (conn-refused)
PORT   STATE SERVICE
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 0.13 seconds

````
Portti 80 on auki edellisestä tehtävästä (ffufme).

<code>-sT localhost</code> parametri määrittää Nmapin suorittamaan TCP-yhteyskävelyskannauksen eli Nmap yrittää muodostaa täydellisen TCP-yhteyden jokaisen skannatun kohteen kanssa. Se käyttää TCP Three-Way Handshakea jokaisen tarkastellun portin kohdalla.

Wireshark:

<img src="/images/wire1.png" alt="wire" title="wire" width="70%" height="70%">

Kuvasta näkyy, että Three-Way Handshake on tapahtunut onnistuneesti porttiin 80 <code>[SYN]</code>, <code>[SYN, ACK]</code>, <code>[ACK]</code>. Muista porteista ei saada tuloksia.

### d) nmap TCP SYN "used to be stealth" scan, -sS (tätä käytetään skannatessa useimmin)

Ajoin komennon <code>sudo nmap -sS localhost</code>. Tuloste:

`````
Starting Nmap 7.94 ( https://nmap.org ) at 2023-11-05 00:37 EET
mass_dns: warning: Unable to determine any DNS servers. Reverse DNS is disabled. Try using --system-dns or specify valid servers with --dns-servers
Nmap scan report for localhost (127.0.0.1)
Host is up (0.0000090s latency).
Other addresses for localhost (not scanned): ::1
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 0.14 seconds
`````

Sain seuraavat tulokset Wiresharkista:

<img src="/images/wire2.png" alt="wire" title="wire" width="70%" height="70%">

Tuloksista näkyy, että Three-Way Handshake on aloitettu ja porttiin 80 on lähetetty SYN-lippu. Tähän on myös vastattu SYN/ACK-lipulla, mutta viimeisestä vaiheetta (ACK) ei tapahdukaan. Muista porteista ei saada tuloksia.

### e) nmap ping sweep -sn

Ajoin komennon <code>nmap -sn localhost</code>. Tuloste:
````
Starting Nmap 7.94 ( https://nmap.org ) at 2023-11-05 01:03 EET
mass_dns: warning: Unable to determine any DNS servers. Reverse DNS is disabled. Try using --system-dns or specify valid servers with --dns-servers
Nmap scan report for localhost (127.0.0.1)
Host is up (0.0012s latency).
Other addresses for localhost (not scanned): ::1
Nmap done: 1 IP address (1 host up) scanned in 0.01 seconds

````
Sain seuraavat tulokset Wiresharkista:

<img src="/images/wire3.png" alt="wire" title="wire" width="70%" height="70%">

Nähdään taas onnistunut Three-Way Handshake portissa 80, mutta oletin näkeväni ICMP-pakettien liikettä. Voi olla mahdollista, että ICMP-pakettien lähetys on estetty.


### f) nmap don't ping -Pn

Suoritetaan skannaus ilman ping-testausta: <code>nmap -Pn localhost</code>. Tuloste:
`````
Starting Nmap 7.94 ( https://nmap.org ) at 2023-11-05 01:18 EET
mass_dns: warning: Unable to determine any DNS servers. Reverse DNS is disabled. Try using --system-dns or specify valid servers with --dns-servers
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00063s latency).
Other addresses for localhost (not scanned): ::1
Not shown: 999 closed tcp ports (conn-refused)
PORT   STATE SERVICE
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 0.14 seconds
`````
Sain seuraavat tulokset Wiresharkista:

<img src="/images/wire4.png" alt="wire" title="wire" width="70%" height="70%">

Koska Nmap ei tee ping-testiä, kuuluisi vain näkyä TCP-pyyntöjä. Ja siltä tulos näyttäisi.

### g) nmap version detection -sV (esimerkki yhdestä palvelusta yhdessä portissa riittää)
Suoritetaan skannaus komennolla <code>nmap -sV -p 80 localhost</code>. Tuloste:
````
Starting Nmap 7.94 ( https://nmap.org ) at 2023-11-05 01:32 EET
mass_dns: warning: Unable to determine any DNS servers. Reverse DNS is disabled. Try using --system-dns or specify valid servers with --dns-servers
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00022s latency).
Other addresses for localhost (not scanned): ::1

PORT   STATE SERVICE VERSION
80/tcp open  http    nginx 1.18.0 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.55 seconds
````
Nmap tunnisti palvelun käyttöjäsrjestelmän (Ubuntu).

Sain seuraavat tulokset Wiresharkista:

<img src="/images/wire5.png" alt="wire" title="wire" width="70%" height="70%">

En huomannut mitään mikä olisi kiinnittänyt huomioni. Tuloksena TCP-pyyntöjä, joita jo aikaisemmin näkyi.

### h) nmap output files -oA foo. Miltä tiedostot näyttävät? Mihin kukin tiedostotyyppi sopii?
Suoritin komennon <code>nmap -oA foo localhost</code> ja Nmap tallensi minulle tiedostot <code>foo.gnmap</code>, <code>foo.nmap</code> ja <code>foo.xml</code>.
- <code>foo.gnmap</code> samankaltainen kuin foo.nmap, mutta se on tarkoitettu käytettäväksi ohjelmallisesti. Se on yleensä tarkoitettu erilaisiin skripteihin tai automatisoituun analyysiin.
- <code>foo.nmap</code> sisältää skannauksen yksityiskohtaiset tulokset (kohdelaitteiden IP-osoitteet, avoimet portit, palveluiden versiot, käytetyt skannaustyypit ja muut yksityiskohdat) Nmapin omassa tekstiformaatissa. Käytetään yksityiskohtaisten skannatiedostojen tallentamiseen ja käsin tehtävään analyysiin.
- <code>foo.xml</code> sisältää skannauksen tulokset XML-muodossa. XML-tiedosto on rakenteellinen ja helposti parsittava tiedostomuoto, jota voidaan käyttää erilaisissa automatisoiduissa prosesseissa.

### i) nmap ajonaikaiset toiminnot (man nmap: runtime interaction): verbosity v/V, help ?, packet tracing p/P, status s (ja moni muu nappi)

Ensiksi ajoin komennon <code>nmap localhost</code> ja painoin "v". Tuloste:

````
Starting Nmap 7.94 ( https://nmap.org ) at 2023-11-05 02:31 EET
mass_dns: warning: Unable to determine any DNS servers. Reverse DNS is disabled. Try using --system-dns or specify valid servers with --dns-servers
Verbosity Increased to 1.
Verbosity Increased to 2.
Discovered open port 39377/tcp on 127.0.0.1
Completed Connect Scan at 02:31, 3.20s elapsed (65535 total ports)
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00018s latency).
Other addresses for localhost (not scanned): ::1
Scanned at 2023-11-05 02:31:09 EET for 3s
Not shown: 65533 closed tcp ports (conn-refused)
PORT      STATE SERVICE
80/tcp    open  http
39377/tcp open  unknown

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 3.30 seconds
````
Tämä nosti skannauksen tulostustasoa ja tulosti yksityiskohtaisemmin kuin tavallisessa skannauksessa. 

Seuraavaksi ajoin saman komennon ja painoin "p". Tuloste:
`````
Starting Nmap 7.94 ( https://nmap.org ) at 2023-11-05 02:28 EET
mass_dns: warning: Unable to determine any DNS servers. Reverse DNS is disabled. Try using --system-dns or specify valid servers with --dns-servers
Packet Tracing enabled.
CONN (0.2741s) TCP localhost > 127.0.0.1:2146 => Operation now in progress
CONN (0.2743s) TCP localhost > 127.0.0.1:2966 => Operation now in progress
CONN (0.2744s) TCP localhost > 127.0.0.1:47313 => Operation now in progress
CONN (0.2745s) TCP localhost > 127.0.0.1:34842 => Operation now in progress
CONN (0.2746s) TCP localhost > 127.0.0.1:58131 => Operation now in progress
CONN (0.2747s) TCP localhost > 127.0.0.1:60876 => Operation now in progress
CONN (0.2748s) TCP localhost > 127.0.0.1:31162 => Operation now in progress
CONN (0.2749s) TCP localhost > 127.0.0.1:23987 => Operation now in progress
CONN (0.2750s) TCP localhost > 127.0.0.1:24926 => Operation now in progress
CONN (0.2751s) TCP localhost > 127.0.0.1:31660 => Operation now in progress
CONN (0.2752s) TCP localhost > 127.0.0.1:31351 => Operation now in progress
CONN (0.2753s) TCP localhost > 127.0.0.1:23682 => Operation now in progress
CONN (0.2754s) TCP localhost > 127.0.0.1:39068 => Operation now in progress
CONN (0.2760s) TCP localhost > 127.0.0.1:5747 => Operation now in progress
CONN (0.2761s) TCP localhost > 127.0.0.1:59095 => Operation now in progress
....MORE....
`````
Packet Tracing -toiminto otettiin käyttöön ja se näyttää lähetyt ja vastaanotetut paketit reaaliajassa.

### j) Ninjojen tapaan. Piiloutuuko nmap-skannaus hyvin palvelimelta?
"Vinkkejä: Asenna Apache. Aja nmap-versioskannaus -sV tai -A omaan paikalliseen weppipalvelimeen. Etsi Apachen lokista tätä koskevat rivit. Wiresharkissa "http" on kätevä filtteri, se tulee siihen yläreunan "Apply a display filter..." -kenttään. Nmap-ajon aikana p laittaa packet tracing päälle. Vapaaehtoinen lisäkohta: jääkö Apachen lokiin jokin todiste nmap-versioskannauksesta?"

Asensin Apachen ja käynnistin sen. Lokitiedoista näkee Nmapin lähettämät HTTP-pyynnöt:
`````
$ cat /var/log/apache2/access.log
127.0.0.1 - - [05/Nov/2023:02:45:36 +0200] "GET / HTTP/1.0" 200 10975 "-" "-"
127.0.0.1 - - [05/Nov/2023:02:45:36 +0200] "GET /nmaplowercheck1699145136 HTTP/1.1" 404 451 "-" "Mozilla/5.0 (compatible; Nmap Scripting Engine; https://nmap.org/book/nse.html)"
127.0.0.1 - - [05/Nov/2023:02:45:36 +0200] "POST /sdk HTTP/1.1" 404 451 "-" "Mozilla/5.0 (compatible; Nmap Scripting Engine; https://nmap.org/book/nse.html)"
127.0.0.1 - - [05/Nov/2023:02:45:36 +0200] "GET / HTTP/1.0" 200 10975 "-" "-"
127.0.0.1 - - [05/Nov/2023:02:45:36 +0200] "GET /evox/about HTTP/1.1" 404 451 "-" "Mozilla/5.0 (compatible; Nmap Scripting Engine; https://nmap.org/book/nse.html)"
127.0.0.1 - - [05/Nov/2023:02:45:36 +0200] "GET /HNAP1 HTTP/1.1" 404 451 "-" "Mozilla/5.0 (compatible; Nmap Scripting Engine; https://nmap.org/book/nse.html)"
127.0.0.1 - - [05/Nov/2023:02:45:36 +0200] "GET / HTTP/1.0" 200 10975 "-" "-"
127.0.0.1 - - [05/Nov/2023:02:45:36 +0200] "GET / HTTP/1.1" 200 10956 "-" "-"
127.0.0.1 - - [05/Nov/2023:02:46:59 +0200] "GET / HTTP/1.0" 200 10975 "-" "-"
127.0.0.1 - - [05/Nov/2023:02:46:59 +0200] "GET / HTTP/1.0" 200 10975 "-" "-"
127.0.0.1 - - [05/Nov/2023:02:46:59 +0200] "GET /nmaplowercheck1699145219 HTTP/1.1" 404 451 "-" "Mozilla/5.0 (compatible; Nmap Scripting Engine; https://nmap.org/book/nse.html)"
127.0.0.1 - - [05/Nov/2023:02:46:59 +0200] "POST /sdk HTTP/1.1" 404 451 "-" "Mozilla/5.0 (compatible; Nmap Scripting Engine; https://nmap.org/book/nse.html)"
127.0.0.1 - - [05/Nov/2023:02:46:59 +0200] "GET /evox/about HTTP/1.1" 404 451 "-" "Mozilla/5.0 (compatible; Nmap Scripting Engine; https://nmap.org/book/nse.html)"
127.0.0.1 - - [05/Nov/2023:02:46:59 +0200] "GET /HNAP1 HTTP/1.1" 404 451 "-" "Mozilla/5.0 (compatible; Nmap Scripting Engine; https://nmap.org/book/nse.html)"
127.0.0.1 - - [05/Nov/2023:02:46:59 +0200] "GET / HTTP/1.0" 200 10975 "-" "-"
127.0.0.1 - - [05/Nov/2023:02:46:59 +0200] "GET / HTTP/1.1" 200 10956 "-" "-"
`````
### k) UDP-skannaus. UDP-skannaa paikkalinen kone (-sU). "Mulla olis vitsi UDP:sta, mutta en tiedä menisikö se perille":

Annoin komennon <code>sudo nmap -sU localhost</code>. Tuloste:
`````
Starting Nmap 7.94 ( https://nmap.org ) at 2023-11-05 02:56 EET
mass_dns: warning: Unable to determine any DNS servers. Reverse DNS is disabled. Try using --system-dns or specify valid servers with --dns-servers
Nmap scan report for localhost (127.0.0.1)
Host is up (0.000010s latency).
Other addresses for localhost (not scanned): ::1
All 1000 scanned ports on localhost (127.0.0.1) are in ignored states.
Not shown: 1000 closed udp ports (port-unreach)

Nmap done: 1 IP address (1 host up) scanned in 0.38 seconds
`````
Wireshark:
<img src="/images/wire6.png" alt="wire" title="wire" width="70%" height="70%">

Näyttäisi siltä, että UDP-skannaus tehtiin, mutta pyynnöt eivät mene perille.

### l) Miksi UDP-skannaus on hankalaa ja epäluotettavaa? Miksi UDP-skannauksen kanssa kannattaa käyttää --reason flagia ja snifferiä? (tässä alakohdassa vain vastaus viitteineen, ei tarvita testiä tietokoneella)

UDP ei tarjoa virheenkorjausta, joten se on epäluotettava protokolla. Toisin sanoen pakettien toimitusta ei taata. UDP-datagrammit lähetetään ilman kuittausta. Koska lähettäjän ja vastaanottajan välillä ei ole virtuaalista yhteyttä, UDP:n sanotaan olevan myös yhteydetön (ScienceDirect, 2023, https://www.sciencedirect.com/topics/computer-science/user-datagram-protocol). 

UDP-skannauksen kanssa --reason-lippu on hyödyllinen, koska se tarjoaa lisätietoja skannauksen tuloksista. Kun UDP-palvelut eivät vastaa skannaukseen, --reason-lippu voi sisältää virheviestejä tai muita selityksiä, jotka auttavat ymmärtämään, miksi yhteys epäonnistui (ChatGPT, https://chat.openai.com/). 

Snifferiä käytetään UDP-skannauksessa tarkkailemaan verkossa kulkevaa tietoliikennettä. UDP-pakettien liikenteen seuraaminen snifferin avulla auttaa havaitsemaan palvelinvasteita, jotka eivät välttämättä näy skannausraporteissa (ChatGPT, https://chat.openai.com/).


## Lähteet


Karvinen, Tero: Oppitunnit 2023-10-23, Tunkeutumistestaus, h2-Hacker Warmup (https://terokarvinen.com/2023/eettinen-hakkerointi-2023/#h2-sniff-n-scan)

Karvinen, Tero: Find Hidden Web Directories - Fuzz URLs with ffuf, https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories

Karvinen, Tero: Find Hidden Web Directories - Fuzz URLs with ffuf
, https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/?fromSearch=ffuf#your-turn---challenge

Karvinen, Tero: Fuffme - Install Web Fuzzing Target on Debian,
https://terokarvinen.com/2023/fuffme-web-fuzzing-target-debian/

HelSec, 0x03 Still Fuzzing Faster (U Fool) - joohoi - HelSec Virtual meetup #1, https://www.youtube.com/watch?v=mbmsT3AhwWU

Nmap, Port Scanning Basics, https://nmap.org/book/man-port-scanning-basics.html

Nmap, Port Scanning Techniques, https://nmap.org/book/man-port-scanning-techniques.html

ScienceDirect, 2023, https://www.sciencedirect.com/topics/computer-science/user-datagram-protocol


