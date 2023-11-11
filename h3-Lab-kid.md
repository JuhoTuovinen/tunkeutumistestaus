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


- Velu 2022: [Mastering Kali Linux for Advanced Penetration Testing 4ed: Chapter 10 - Exploitation](https://learning.oreilly.com/library/view/mastering-kali-linux/9781801819770/Text/Chapter_10.xhtml#_idParaDest-257):


## a) Asenna Kali virtuaalikoneeseen
## b) Asenna Metasploitable 2 virtuaalikoneeseen
Menin ohjeiden mukaan: https://docs.rapid7.com/metasploit/metasploitable-2/
Latasin sen täältä: https://sourceforge.net/projects/metasploitable/postdownload





## c) Tee koneille virtuaaliverkko, jossa
- Kali saa yhteyden Internettiin, mutta sen voi laittaa pois päältä
- Kalin ja Metasploitablen välillä on host-only network, niin että porttiskannatessa ym. koneet on eristetty intenetistä, mutta ne saavat yhteyden toisiinsa
- Osoita eri komennoilla, että Internet-yhteys katkeaa: 'ping 1.1.1.1', 'ping www.google.com', 'curl www.google.com'
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



