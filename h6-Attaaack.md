# Attaaack
Juho Tuovinen

Tässä raportissa aioin kertoa kuinka suoritin tehtävät, jotka on annettu Haaga Helian Tunkeutumisteustaus kurssilla. Tehtävät löytyvät [täältä](https://terokarvinen.com/2023/eettinen-hakkerointi-2023/#h6-attaaack).

Tietokoneen speksit, jolla suoritan toimenpiteen:
- Apple MacBook 2015
- macOs Monterey versio 12.7
- Ram -muistia 16GB
- levytilaa/muistia vapaana 11,96 GB.

Ohjelmistot
- UTM Version 4.1.6 (75)
- VM OS: Kali Linux 2023.3 (kali-rolling)


## Lue/katso/kuuntele ja tiivistä. (Tässä x-alakohdassa ei tarvitse tehdä testejä tietokoneella, vain lukeminen tai kuunteleminen ja tiivistelmä riittää. Tiivistämiseen riittää muutama ranskalainen viiva.)

- Yehoshua and Kosayev 2021: [Antivirus Bypass Techniques](https://learning.oreilly.com/library/view/antivirus-bypass-techniques/9781801079747/), luku:
  - [Chapter 1: Introduction to the Security Landscape](https://learning.oreilly.com/library/view/antivirus-bypass-techniques/9781801079747/B17257_01_Epub_AM.xhtml#_idParaDest-18)
- Halonen, Rajala ja Ollikainen 2023: [PhishSticks Youtube Channel](https://www.youtube.com/@phishsticks_pentest/videos), kahdeksan videota, yhteensä noin 15 min
- Halonen, Rajala ja Ollikainen 2023: [PhishSticks Git Repository](https://github.com/therealhalonen/PhishSticks/), sivut:
  - [README.md](https://github.com/therealhalonen/PhishSticks/tree/master) (Aikajärjestyksessä, jos aloitat pohjalta. Kuvailee, miten näitä tekniikoita opeteltiin ja kehitettiin)
  - [Revshell](https://github.com/therealhalonen/PhishSticks/tree/master/payloads/revshell)
  - [Mitigations](https://github.com/therealhalonen/PhishSticks/blob/master/documentation/Mitigations.md)
  - [Installing Windows 10 on a virtual machine](https://github.com/therealhalonen/PhishSticks/blob/master/notes/ollikainen/windows.md)
- [MITRE Att&ck Frequently Asked Questions](https://attack.mitre.org/resources/faq/): Part 1. General
  - Erityisesti kehikon omat määritelmät termeille tactics, techniques and procedures
- [MITRE Att&ck Enterprise Matrix](https://attack.mitre.org/)
  - Silmäile, poimi muutama esimerkki. Koko kehikko on laaja, eikä sitä tarvitse lukea tässä kokonaan.

## a) The OS pwns you. Asenna Windows virtuaalikoneeseen samaan verkkoon hyökkäyskoneen (esim. Kali, Debian) kanssa. Kokeile, että saat koneen irrotettua Internetistä.


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
