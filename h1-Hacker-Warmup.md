# Hacker Warmup

Juho Tuovinen

Tässä raportissa aioin kertoa kuinka suoritin tehtävät, jotka on annettu Haaga Helian Tunkeutumisteustaus kurssilla. Tehtävät löytyvät [täältä](https://terokarvinen.com/2023/eettinen-hakkerointi-2023/#h1-hacker-warmup).

Tietokoneen speksit, jolla suoritan toimenpiteen:
- Apple MacBook 2015
- macOs Monterey versio 12.7
- Ram -muistia 16GB
- levytilaa/muistia vapaana 49,11 GB.

Ohjelmistot
- UTM Version 4.1.6 (75)
- VM OS: Kali Linux 2023.3 (kali-rolling)

## Lue/katso ja tiivistä.

[Santos et al: The Art of Hacking (Video Collection): [..] 4.3 Surveying Essential Tools for Active Reconnaissance](https://learning.oreilly.com/videos/the-art-of/9780135767849/9780135767849-SPTT_04_00/)

Active reconnaissance on toimintaa, jolla löydetään tietoa tai haavoittuvuuksia kohdejärjestelmästä tai verkosta. Toimintaa on esimerkiski porttiskannaus, web-palveluiden haavoittuvuuksien skannaaminen ja verkon haavoittuvuuksien skannaaminen.

Porttiskannaustyökaluja
- Nmap (monipuolisin työkalu porttiskannaukseen)
- Masscan (nopea, muttei monipuolinen) voi skannata koko inernetin minuuteissa.
- Udpprotoscanner (nopea UDP- porttiskanneri, skannaa vain UDP-portit)

Web-skannaustyökaluja
- EyeWitness (skannaa web-sovellukset ja verkkosivut, ja ottaa kuvakaappaukset)

Verkkohaavoittuvuuskannereita:
- OpenVAS
- Nessus
- Nexpose
- Qualys
- Nmap (toimii jossainmäärin haavoittuvuuskannerina)

Web-haavoittuvuuskannereita:
- Burp Suite
- Nikto
- WPScan
- SQLMap
- Zed Attack Proxy

  
[Hutchins et al 2011: Intelligence-Driven Computer Network Defense Informed by Analysis of Adversary Campaigns and Intrusion Kill Chains](https://lockheedmartin.com/content/dam/lockheed-martin/rms/documents/cyber/LM-White-Paper-Intel-Driven-Defense.pdf), chapters Abstract, 3.2 Intrusion Kill Chain

- Perinteiset verkkoturvakeinot eivät riitä Advanced Persistent Threats (APT) -hyökkäysten torjumiseen, sillä ne pyrkivät vaarantamaan sensitiivistä tietoa
- Kill Chain on uusi tiedustelupohjainen tietokoneverkon puolustusmalli, joka perustuu uhkien kohdennettuun analyysiin ja puolustuksen lieventämiseen, tarjoten etulyöntiaseman APT-tason vastustajia vastaan. Malli mahdollistaa hyökkäysten analysoinnin vaiheittain: tiedustelu, aseistaminen, toimitus, hyväksikäyttö, asennus, komento ja hallinta sekä tavoitteiden toteuttaminen
- Kill chain -malli antaa rakenteen hyökkäysten analysointiin ja puolustustoimenpiteiden suuntaamiseen, ja se auttaa puolustajia priorisoimaan investointejaan
- Indikaattorit jaetaan kolmeen tyyppiin: atomiset, laskennalliset ja käyttäytymisperusteiset, ja ne ovat avainasemassa puolustuksen suunnittelussa
- Kampanja-analyysi auttaa tunnistamaan samankaltaisuuksia useiden hyökkäysten välillä ja ymmärtämään hyökkääjien toimintamalleja ja taktiikoita
- Kill chain tarjoaa tavan ymmärtää hyökkäysten etenemistä ja auttaa puolustajia toimimaan ennakoivasti hyökkääjien liikkeisiin nähden



## Ratkaise Over The Wire: Bandit kolme ensimmäistä tasoa (0-2).

Taso 0. Kirjauduin sisään peliin käyttäen SSH-yhteyttä.

    ssh bandit0@bandit.labs.overthewire.org -p 2220

<img src="/images/taso0.png" alt="wire" title="wire" width="70%" height="70%">

Taso 1. Löysin salasanan tiedostosta "readme", käyttämällä komentoja <code> ls </code> ja <code> cat readme </code>.

<img src="/images/taso1.png" alt="wire" title="wire" width="70%" height="70%">

Kirjauduin sisään toiselle käyttäjälle "bandit1" ja käytin äsken saamaani salasanaa.

    ssh bandit1@bandit.labs.overthewire.org -p 2220   

<img src="/images/taso2.png" alt="wire" title="wire" width="70%" height="70%">

Taso 2. Salasana löytyy tiedostosta "-". Avasin tiedoston.

    cat ./-

<img src="/images/taso3.png" alt="wire" title="wire" width="70%" height="70%">

Tehtävään hain apua [StackOverflow](https://stackoverflow.com/questions/42187323/how-to-open-a-dashed-filename-using-terminal):sta.

## Ratkaise Challenge.fi:stä yksi tehtävä

Valitsin [Challenge.fi 2021](https://2021.challenge.fi/challenges) tehtävän OSINT:GEOINT1, koska se vaikutti mielenkiintoiselta. Sain kuvan ja vihjeet "Land of clogs & tulips" ja "Near Centraal Station". Minusta kuvan maisema muistutti Alankomaita ja Alankomaissa puukengät ja tulppaanit ovat perinteisiä symboleita ja Amsterdamin päärautatieasema on Centraal Station.

<img src="/images/geoint.png" alt="wire" title="wire" width="70%" height="70%">

Geo-kuva:

<img src="/images/osint1.PNG" alt="wire" title="wire" width="70%" height="70%">
 
Laitoin kuvan Googlen käänteiseen kuvahakuun, mikä ehdotti minulle kuvia Amsterdamista. Seuraavaksi tätytyy selvittää, mikä katu on kyseessä. Kuvasta huomasin, että kadulla on hotellin kyltti "Hotel CC", joten Googletin hotellin. Google antoi hotellin sijainniksi "Warmoesstraat 42, 1012 JE Amsterdam", joten kokeilin kadun nimeä tehtävään ja vastaus oli oikea.

<img src="/images/hotelcc.png" alt="wire" title="wire" width="70%" height="70%">

## c) Ratkaise PortSwigger Labs: Lab: SQL injection vulnerability in WHERE clause allowing retrieval of hidden data

Tehtävä löytyy sivulta https://portswigger.net/web-security/sql-injection/lab-retrieve-hidden-data. Tehtävän tarkoituksena on saada selville verkkokaupan julkaisematomia tuotteita, jotka eivät kuuluisi olla saatavilla. Tarkoitus on tehdä SQL-injektio käyttämällä URL-syöttöä. 

Verkkosivu ennen hyökkäystä:

<img src="/images/shop1.png" alt="wire" title="wire" width="70%" height="70%">

Verkkosivu hyökkäyksen jälkeen:

<img src="/images/shop2.png" alt="wire" title="wire" width="70%" height="70%">

Lisäsin URL:in loppun <code> '+OR+1=1-- </code>. Tämä suoritti SQL-kyselyn <code>SELECT * FROM products WHERE category = 'Gifts' OR 1=1--' AND released = 1</code>, joka paljastaa julkaisemattomat tuotteet, sillä kysely palauttaa kaikki kohteet, joissa joko kategoria on "Gifts" tai 1 on yhtä suuri kuin 1. Koska 1=1 on aina tosi, kysely palauttaa kaikki kohteet.

käytin tehtävässä apuna: https://portswigger.net/web-security/sql-injection#sql-injection-examples.

## d) Asenna Linux virtuaalikoneeseen.

Minulla on jo ennestään asennetuna Kali Linux 2023.3 (kali-rolling) virtuaalikone. Asennustiedosto on ladattu sivustolta https://www.kali.org/. Virtuaalikone on asennettu UTM-virtualisointiohjelmistoon.

Speksit:
````
PRETTY_NAME="Kali GNU/Linux Rolling"
NAME="Kali GNU/Linux"
VERSION_ID="2023.3"
VERSION="2023.3"
VERSION_CODENAME=kali-rolling
ID=kali
ID_LIKE=debian
HOME_URL="https://www.kali.org/"
SUPPORT_URL="https://forums.kali.org/"
BUG_REPORT_URL="https://bugs.kali.org/"
ANSI_COLOR="1;31"
````

<img src="/images/kali.png" alt="wire" title="wire" width="70%" height="70%">

## e) Porttiskannaa 1000 tavallisinta tcp-porttia omasta koneestasi (localhost). Analysoi tulokset.
Ensiksi katkaisin yhteyden virtuaalikoneestani internetiin varmuuden vuoksi, jotta en vahingossa skannaisi muiden laitteita tai verkkoja. Tarkistin sen pingaamalla googlen nimipalvelinta <code> ping google.com </code> ja myös selaimesta, että verkkoon ei saada yhteyttä. Seuraavaksi skannaan portit 1-1000.

    sudo nmap -p 1-1000 localhost
    

- <code> sudo </code>ajetaan komento pääkäyttäjänä
- <code> nmap </code>ajetaan käyttäen työkalua nmap
- <code> -p 1-1000 </code> skannataan portit 1-1000
- <code> local host </code> skannataan portit omalta koneelta

<img src="/images/nmap1000.png" alt="wire" title="wire" width="70%" height="70%">

Nmapin raportista nähdään esimerkiksi:
- Versio: Skannauksessa käytetty Nmap-versio on 7.94
- Aloitusaika: Skannaus aloitettiin 27. lokakuuta 2023 kello 16:57 paikallista aikaa (EEST)
- DNS-varoitus: Nmap ei pystynyt määrittämään DNS-palvelimia automaattisesti ja käänteinen DNS-tarkistus on pois käytöstä
- Kohde: Skannaus suoritettiin localhost-osoitteeseen 127.0.0.1
- Hostin tila: Localhost (127.0.0.1) on ylhäällä ja vastaa noin 0.0000050 sekunnin viiveellä
- Muut osoitteet: Muita osoitteita localhostille (::1) ei skannattu
- Porttitila: Kaikki 1000 skannattua porttia localhostilla ovat "ignored states" -tilassa, mikä tarkoittaa, että Nmap ei voinut määrittää porttien tilaa ja ei niitä siksi näytä
- Yhteenveto: Skannaus suoritettiin yhdelle IP-osoitteelle (localhost) ja yksi hosti havaittiin olevan aktiivinen. Skannaus kesti 0.11 sekuntia

## f) Porttiskannaa kaikki koneesi (localhost) tcp-portit. Analysoi tulokset. (Edellisissä kohdissa mainittuja analyyseja ei tarvitse toistaa, voit vain viitata niihin ja keskittyä eroihin).

Seuraavaksi skannasin kaikki portit koneeltani.

    sudo nmap -p- localhost

<img src="/images/nmapf.png" alt="wire" title="wire" width="70%" height="70%">

Raportti on lekein sama kuin aikaisemmin. Kuitenkin nähdään, että portteja on skannattu yhteensä 65535 ja skannaus kesti hieman pidempään (yhteensä 1.13 sekuntia).

## g) Tee laaja porttiskanaus (nmap -A) omalle koneellesi (localhost), kaikki portit. Selitä, mitä -A tekee. Analysoi tulokset. (Edellisissä kohdissa mainittuja analyyseja ei tarvitse toistaa, voit vain viitata niihin ja keskittyä eroihin.).

Skannasin 1000 tavallisinta TCP-porttia ja -A -parametri suorittaa laajan skannauksen, joka sisältää versionkyselyn, OS-tunnistuksen, sekä lisää muita kattavia skannaustoimintoja, kuten päivitystietojen hakemisen ja skannatun palvelimen haavoittuvuuksien arvioinnin.

    sudo nmap -A localhost

<img src="/images/nmapg.png" alt="wire" title="wire" width="70%" height="70%">

Tulokset olivat taas amanlaisia kuin aikaisemmin 1000 porttia skannatessa. Skannaus kesti yhteensä 2.22 sekuntia. Uusia huomioita:

- Liian monta sormenjälkeä: Nmap ei pystynyt antamaan tarkkoja tietoja käyttöjärjestelmästä, koska liian moni käyttöjärjestelmä vastasi havaittuja ominaisuuksia
- Verkon etäisyys: Skannaus suoritettiin omalta koneeltani (localhost), joten etäisyys verkon kautta oli 0 hyppyä
- Käyttöjärjestelmä- ja palvelutunnistus: Skannauksessa suoritettiin käyttöjärjestelmä- ja palvelutunnistus, mikä tarkoittaa, että Nmap yritti tunnistaa käyttöjärjestelmän ja palvelut, jotka kuuntelevat avoimia portteja.

## h) Asenna ja käynnistä jokin palvelin (apache, ssh...) koneellesi. Vertaile, miten porttiskannauksen tulos eroaa.

Asensin Apache-palvelimen.

    sudo apt install apache2

Sen jälkeen suoriti porttiskannin kaikkille porteille koneellani.

    sudo nmap -p- loclhost

<img src="/images/nmaph.png" alt="wire" title="wire" width="70%" height="70%">

Raportista nähdään, että portti 80 on auki HTTP-palvelimelle.

## i) Kokeile ja esittele jokin avointen lähteiden tiedusteluun sopiva weppisivu tai työkalu.

Kokeilin työkalua nimeltä Sherlock (https://github.com/sherlock-project/sherlock). Sherlock on Python-pohjainen OSINT-työkalu, joka on suunniteltu etsimään käyttäjätilejä ja muita online-tunnisteita eri sosiaalisen median ja verkkopalveluiden alustoilta. Käyttäjänimen syöttämällä työkalu etsii eri alustoilta (GitHub, Instagram, Twitter jne.) samannimistä käyttäjää. Sherlockin toiminnallisuus perustuu hakukoneiden ja verkkopalveluiden rajapintoihin sekä avoimiin tietolähteisiin. Se ei vaadi kirjautumista mihinkään palveluun, eikä se ylitä palveluiden antamia hakurajoituksia.

Katsoin asennusohjeet GitHubista (https://github.com/sherlock-project/sherlock) ja etenin niiden mukaan. Kokeilin hakemalla itseäni, käyttäen käyttäjänimeä juhotuovinen, joka minulla on käytössä muutamalla alustalla.

    pyhton3 sherlock juhotuovinen

<img src="/images/sherlock.png" alt="wire" title="wire" width="70%" height="70%">

Kuvasta tummensin pois haut, jotka mahdollisesti olivat jonkun toisen, tai en tunnistanut niitä omaksi. Tuloksesta näemme, että Sherlock löysin käyttäjänimeni esim. GitHubista, Replitistä, Twitteristä ja freecodecamp.com:sta.

Huomioita: kokeiltuani työkalua, huomasin, että työkalu mahdollisesti "mainostaa" tiettyjä sivustoja, eikä haku ole tarkka. Kokeiltuani hakuun useita käyttäjänimiäni, huomasin, että Sherlock tarjoaa samoja sivustoja, jossa ei olisi kyseistä käyttäjää olemassa, eikä näytä profiilia vaan menee sivuston etusivulle. Työkalua käyttäessä täytyy myös pitää mielessä, että jollakin toisella henkilöllä voi olla käytössä käyttämäsi käyttäjänimi ja voi näyttää siis muidenkin henkilöiden profiileja kuin omasi tai sen käyttäjän, jota olet etsimässä.

## j) Vapaaehtoinen: Tee lisää harjoituksia alustoilta, joihin tässä on tutustuttu

Tein challenge.fi vuoden 2021 tehtävät OSINT-alueelta. Tehtävät olivat mielenkiintoisia ja tietoa täytyi hakea internetin syövereistä. Vinkit auttoivat myös, jos ei meinannut tietoa löytyä. Tehtävissä, jossa täytyi tunnistaa sijainti tai esine kuvan avulla, käytin google käänteistä kuvahakua, joka osoittautui erittäin hyväksi työkaluksi näissä tehtävissä.

<img src="/images/challenge.png" alt="wire" title="wire" width="70%" height="70%">

## Lähteet


Karvinen, Tero: Oppitunnit 2023-10-23, Tunkeutumistestaus, h1-Hacker Warmup (https://terokarvinen.com/2023/eettinen-hakkerointi-2023/#h1-hacker-warmup)

ChatGPT, https://chat.openai.com

Cahllenge.fi, GenZ Hack Challenge 2021, https://2021.challenge.fi/challenges

PortSwigger, SQL injection, https://portswigger.net/web-security/sql-injection#sql-injection-examples

Kali, https://www.kali.org/






