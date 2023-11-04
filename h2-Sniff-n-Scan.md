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
- [Port Scanning Techniques](https://nmap.org/book/man-port-scanning-techniques.html)

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

Ennen Ffuf:n käyttöä kytken virtuaalikoneen pois verkosta. KOkeilin manuaalisesti löytyisikö sivulta <code> /admin </code>, <code> /secret </code> tai esim <code> /robots.txt </code>, mutta ei löytynyt. Kokeilin ensiksi komentoa <code> ./ffuf -w common.txt -u http://127.0.0.2:8000/FUZZ</code>, jossa käytän aikaisemmin ladattua sanalistaa.


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

Tulosteesta nähdään, että jokaisen ei-toivotun vastauksen koko on 154 tavua eli 154 ASCII-merkkiä. Filteröin käyttäen apuna tätä tietoa komennolla <code> ./ffuf -w common.txt -u http://127.0.0.2:8000/FUZZ -fs 154 </code>. Tulosteesta löytyi seuraavat tiedot:

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
<code> /wp-admin</code>-kansio kuullosti kiinnostavimmalta, joten liitin sen URL:iin <code>http://127.0.0.2:8000/wp-admin</code>. TADAA! Oikeasivu ja lippu löytyi!
<img src="/images/flag.png" alt="" title="" width="70%" height="70%">

Lippu: FLAG{tero-wpadmin-3364c855a2ac87341fc7bcbda955b580}

## b) Fuffme. Asenna [Ffufme](https://terokarvinen.com/2023/fuffme-web-fuzzing-target-debian/) harjoitusmaali paikallisesti omalle koneellesi. Ratkaise tehtävät (kaikki paitsi ei "Content Discovery - Pipes")

Asensin Fuff:in ja Fuffme:n artikkelin ohjeiden mukaan. Otin koneeni pois netistä ja aloitin testaamisen. 

### Basic Content Discovery
Komennolla <code> ffuf -w $HOME/wordlists/common.txt -u http://localhost/cd/basic/FUZZ </code> löysin halutut kansiot <code>class</code> ja <code>development.log</code>.

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

Tehtävän suomennos: "Olemme törmänneet hakemistoon nimeltä /logs, mutta sisältöä ei nähdä. Voimme tehdä oletuksen, että tässä hakemistossa olevat tiedostot käyttävät .log-tiedostopäätettä. Käytä skannausta -e-kytkimellä määrittääksesi tiedostopääte, joka lisätään jokaisen sanalistan sanan loppuun oikean lokitiedoston löytämiseksi." Lähdin kokeilemaan annetulla komennolla <code>ffuf -w ~/wordlists/common.txt -e .log -u http://localhost/cd/ext/logs/FUZZ/code>. Skannauksen jälkeen hakemisto <code>/logusers.log</code> pitäisi löytyä.

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

Tehtävän suomennos: "Ideaalimaailmassa kaikki verkkosivustot vastaisivat oikein oikeilla HTTP-tilakoodilla. Kokeillaan ajaa seuraava ffuf-esimerkki ja katsotaan, mitä tapahtuu: <code>ffuf -w ~/wordlists/common.txt -u http://localhost/cd/no404/FUZZ/code>."

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

Ajoin ohjeessa annetun komennon <code>ffuf -w ~/wordlists/common.txt -u http://ffuf.test/cd/rate/FUZZ -mc 200,429/code> ja sain seuraavan syötteen:

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

En saanut toivottua tulosta. Tiedostoa <code>oracle</code> eii löytynyt. Vian selvitys kesken.

### Subdomains - Virtual Host Enumeration

Tehtävän suomennos: "FFUF:ia voidaan käyttää aliverkkotunnusten löytämiseen virtuaalisten isäntien ja Host-otsikon muuttamisen avulla. Kokeile ajaa seuraava FFUF-komento: <code>ffuf -w ~/wordlists/subdomains.txt -H "Host: FUZZ.ffuf.me" -u http://localhost</code>"

Tuloste:

`````
....MORE....
:: Progress: [1907/1907] :: Job [1/1] :: 1459 req/sec :: Duration: [0:00:01] :: Errors: 0
:: Progress: [1907/1907] :: Job [1/1] :: 1459 req/sec :: Duration: [0:00:01] :: Errors: 0
:: Progress: [1907/1907] :: Job [1/1] :: 1459 req/sec :: Duration: [0:00:01] :: Errors: 0

`````
"Kun tarkastelet tuloksia, huomaat, että jokainen tulos palautuu koon ollessa 1495 tavua. Kokeile nyt ajaa alla oleva FFUF-skannaus, mutta tällä kertaa käytä -fs-kytkintä suodattaaksesi pois kaikki tulokset, joiden koko on 1495 tavua: <code>ffuf -w ~/wordlists/subdomains.txt -H "Host: FUZZ.ffuf.me" -u http://localhost -fs 1495/code>"

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

<img src="/images/wire2.png" alt="wire" title="wire" width="70%" height="70%">

Kuvasta näkyy, että Three-Way Handshake on tapahtunut onnistuneesti porttiin 80 <code>[SYN]</code>, <code>[SYN, ACK]</code>, <code>[ACK]</code>.

###d) nmap TCP SYN "used to be stealth" scan, -sS (tätä käytetään skannatessa useimmin)

## Lähteet


Karvinen, Tero: Oppitunnit 2023-10-23, Tunkeutumistestaus, h2-Hacker Warmup (https://terokarvinen.com/2023/eettinen-hakkerointi-2023/#h2-sniff-n-scan)

https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories

https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/?fromSearch=ffuf#your-turn---challenge

https://terokarvinen.com/2023/fuffme-web-fuzzing-target-debian/

https://www.youtube.com/watch?v=mbmsT3AhwWU

https://nmap.org/book/man-port-scanning-basics.html

https://nmap.org/book/man-port-scanning-techniques.html

