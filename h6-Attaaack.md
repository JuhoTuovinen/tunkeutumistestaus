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

käytin aikesempaa ohjetta: https://github.com/therealhalonen/PhishSticks/blob/master/notes/ollikainen/windows.md

latasin English (Great Britain), ISO – Enterprise LTSC, 64-bit edition


Kokeilin asentaa UTM:lle, mutta ei onnistunut. KOkeilien "Virtualize" ja "emulate" -vaihtoehtoja mutta sain aina saman virheen.
Vaihdoin välissä VirtualBoxiin ja siellä tilanne näytti hetken paremmalta; kone lähti käyntiin. Kuitenkin käynnistys oli tavanomaisempaa hitaampaa, joten kone oli pakko poistaa. Sillä ei pystynyt työskentelemään. Muistelin että olen joskus ladannut koneen täältä: https://www.microsoft.com/en-us/software-download/windows10ISO. Ei toiminut, sama virhe. Kokeilin muuttaa Ram muistin määrää sekä levytilna määrää, mutta ei auttanut. 

<img src="/images/error1.png" alt="" title="" width="70%" height="70%">

Muistelin, että olen aikaisemmin Tero Karvisen Infa kurssilla asentanut windows virtuaalikoneen. Löysinkin [raporttini](https://github.com/JuhoTuovinen/linux-course/blob/main/h5-Vaihtoehdot.md), jossa olen koneen asentanut aikaisemmin. Olen kuitenkin ladannut samasta paikasta ISO_tiedoston kuin aikaisemmin ja käyttänyt UTM:ää, mutta kuitenkaan tämä ei nyt toimi. Aioa syy mitä keksin, on levytilan puute. Minulla on nyt 43 gigaa muistia.

Kokeilin hetken päästä asentaa konetta uudelleen ja sain sen toimimaan seuraavilla spekseillä:

<img src="/images/settings1.png" alt="" title="" width="70%" height="70%">
<img src="/images/settings2.png" alt="" title="" width="70%" height="70%">

Toimii!
<img src="/images/win1.png" alt="" title="" width="70%" height="70%">

Virhe johtui siis vähäisestä muistista. Aiemmin testatessa minulla oli muistia 20 gigaa, mutta siivosin konetta ja nostin sitä 40:een ja tämä oli tarpeeksi virtuaalikoneen pyörittämiseen. 

## b) Trustme.lnk. Kokeile PhishSticksin [revshell](https://github.com/therealhalonen/PhishSticks/tree/master/payloads/revshell) vihamielistä tiedostoa, joka avaa käänteisen shellin hyökkääjän koneelle. Selitä, mitä tapahtuu ja miksi. Testaa, että pysyt antamaan kohdekoneelle komentoja reverse shellin kautta.


## d) PageRank. Laita [linkki raporttiisi kurssisivun kommentiksi](https://terokarvinen.com/2023/eettinen-hakkerointi-2023/#comments).
- Kannattaa mainita URL sekä leipätekstissä ("Comment") että kotisivun osoitteessa ("Homepage"), jotta sitä on helppo klikata.
- Yksikin lause sisällöstä tai jostain kiinostavasta huomiosta lisännee klikkauksia
- PhishSticks etsii tästä viitattavia projekteja, joten tästä voi saada useita sisääntulevia linkkejä kerralla.
- Tämä kommentin lisääminen on erittäin suositeltava, mutta vapaaehtoinen. Olet kirjoittanut raportteja tunteja ja päiviä - miksi kirjoittaa pöytälaatikolle?
- Email-kohtaan pitää täyttää joku osoite, jotta se toimii. Voit laittaa tuohon oman osoitteen tai example.com.invalid-osoitteen, sillä osoite ei tule näkyviin.
- Hyväksyn kommentit käsin, ne tulevat siis näkyviin viiveellä.


## c) Attaaack! MITRE Attack Enterprise Matrix: Demonstroi viisi tekniikkaa viidestä eri taktiikasta.
- Tekniikkaa tulee kokeilla käytännössä, kuvailu ei riitä.
- Asenna tarvittaessa omat harjoitusmaalit. Eristä tarvittaessa koneet Internetistä harjoittelun ajaksi.
- Voit käyttää kurssilla jo opeteltuja työkaluja (helpompaa) tai kokeilla uusia. Aiemminkin käytetyistä tekniikoista pitää tehdä uusi demonstraatio tässä tehtävässä.
  - Mikäli haluat tehtävästä helpon version, tekniikoiden valinta auttaa. Tuolta löytyy myös tuttuja ja helppoja tekniikoita.
- Selitä, mitä esimerkissä tapahtuu.
- Nimeä käytetyt taktiikat, tekniikat ja alitekniikat. Merkitse myös numerot T0123.456.
