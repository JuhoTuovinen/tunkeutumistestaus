# Totally Legit Sertificate
Juho Tuovinen

Tässä raportissa aioin kertoa kuinka suoritin tehtävät, jotka on annettu Haaga Helian Tunkeutumisteustaus kurssilla. Tehtävät löytyvät [täältä](https://terokarvinen.com/2023/eettinen-hakkerointi-2023/#h4-totally-legit-sertificate).

Tietokoneen speksit, jolla suoritan toimenpiteen:
- Apple MacBook 2015
- macOs Monterey versio 12.7
- Ram -muistia 16GB
- levytilaa/muistia vapaana 47,35 GB.

Ohjelmistot
- UTM Version 4.1.6 (75)
- VM OS: Kali Linux 2023.3 (kali-rolling)

## x) Lue/katso ja tiivistä. (Tässä x-alakohdassa ei tarvitse tehdä testejä tietokoneella, vain lukeminen tai kuunteleminen ja tiivistelmä riittää. Tiivistämiseen riittää muutama ranskalainen viiva.)

- OWASP 2021: OWASP Top 10:2021
  - [A01:2021 – Broken Access Control](https://owasp.org/Top10/A01_2021-Broken_Access_Control/) (IDOR ja path traversal ovat osa tätä)
    - pääsynhallinta valvoo, että käyttäjät eivät voi toimia lupien ulkopuolella. Jos lupia päästään kiertämään on mahdollista, että valtuuttamattomia tietoja paljastuuu, muokkataan tai tuhotaan, tai toiminnallisuuksien suorittamiseen käyttäjän rajojen ulkopuolella
    - yleisimpiä pääsynhallinnan haavoittuvuuksia: valtuuksien rikkominen, tarkistusten kiertäminen, toisen tilin katselu tai muokkaaminen, puutteellinen pääsynhallinta API-toiminnoissa, oikeuden nosto, metatietojen manipulointi, CORS-konfigurointivirhe, force browsing
    - kuinka estää väärinkäyttö: tehokas pääsynhallinta, käytä luotettavia palvelimia, joissa hyvä koodi, poista web-palvelimen hakemistoluettelo, varmista etteivät tiedostojen metatiedot tai varmuuskopiot ole saatavilla verkkohakemistoissa, kirjaa pääsynhallinnan epäonnistumiset ja ilmoita tarvittaessa, aseta API:n käyttöön rajoituksia
  - [A10:2021 – Server-Side Request Forgery (SSRF)](https://owasp.org/Top10/A10_2021-Server-Side_Request_Forgery_%28SSRF%29/)
    - Server-Side Request Forgery tapahtuu silloin, kun verkkosovellus hakee etäresurssia ilman käyttäjän antaman URL-osoitteen tarkistusta. Tämä mahdollistaa hyökkääjän saada sovellus lähettämään erikoispyyntöjä odottamattomaan paikkaan, vaikka sovellusta olisi suojeltu palomuurilla, VPN:llä tai muilla verkon käyttöoikeusluetteloilla.
    - kuinka suojautua:
      - Verkkokerros:
        - hajauttamalla etäresurssien käyttöominaisuudet eri verkkoihin
        - Zero trust -periaatetta noudattavat palomuuri- tai verkkopääsyvalvontasäännöt, jotka estävät kaiken paitsi välttämättömän intranet-liikenteen
      - Sovelluskerros:
        - puhdista ja validoi kaikki käyttäjän antamat syötteet
        - älä lähetä raakoja vastauksia asiakkaille
        - poista HTTP-uudelleenohjaukset käytöstä
        - älä käytä estoluetteloita, ne voidaan ohittaa
    - Esimerkki skenaario: hyökkääjät voivat päästä käsiksi paikallisiin tiedostoihin tai sisäisiin palveluihin saadakseen arkaluonteista tietoa, kuten file:///etc/passwd ja http://localhost:28017/

- PortSwigger Academy:
  - [Access control vulnerabilities and privilege escalation](https://portswigger.net/web-security/access-control) (IDOR on osa tätä)
  - [Server-side template injection](https://portswigger.net/web-security/server-side-template-injection)https://portswigger.net/web-security/server-side-template-injection
  - [Server-side request forgery (SSRF)](https://portswigger.net/web-security/ssrf)https://portswigger.net/web-security/ssrf
  - [Cross-site scripting](https://portswigger.net/web-security/cross-site-scripting)https://portswigger.net/web-security/cross-site-scripting
    - Cross-site scripting on haavoittuvuus, joka mahdollistaa hyökkääjän vuorovaikutuksen haavoittuvan sovelluksen käyttäjien kanssa
    - voi johtaa erilaisiin haitallisiin seurauksiin, kuten tietojen varastamiseen, istuntojen kaappaamiseen ja käyttäjien manipulointiin
    - haavoittuvuuksia on kolme päätyyppiä: Reflected XSS, Stored XSS ja DOM-based XSS
  
 
- Karvinen 2020: [Using New Webgoat 2023.4 to Try Web Hacking](https://terokarvinen.com/2023/webgoat-2023-4-ethical-web-hacking/)
  - artikkeli kertoo, kuinka asennetaa WebGoat
  - asenna java ja palomuuri
  - lataa uusin webgoat osoitteesta https://github.com/WebGoat/WebGoat/releases/
  - aja webgoat esim. portissa 8888, koska sinulla on mahdollisesti OWASP ZAP jo portissa 8080
  - webgoat on asennettu
  - artikkelin lopusta löytyy muutamat vinkit troubleshootaukseen

## a) Totally Legit Sertificate. Asenna OWASP ZAP, generoi CA-sertifikaatti ja asenna se selaimeesi. Laita ZAP proxyksi selaimeesi. Osoita, että hakupyynnöt ilmestyvät ZAP:n käyttöliittymään. (Ei toimi localhost:lla ilman Foxyproxya)

#### ZAP asennus
- tarkistin, että java on asennettukomennolla <code>java -version</code> ja näyttäisi olevan:
````
Picked up _JAVA_OPTIONS: -Dawt.useSystemAAFontSettings=on -Dswing.aatext=true
openjdk version "17.0.9-ea" 2023-10-17
OpenJDK Runtime Environment (build 17.0.9-ea+6-Debian-1)
OpenJDK 64-Bit Server VM (build 17.0.9-ea+6-Debian-1, mixed mode, sharing)
````
- latasin owasp zap -linux installer virtuaalikoneelle osoitteesta [osoite](https://github.com/zaproxy/zaproxy/releases/download/v2.14.0/ZAP_2_14_0_unix.sh)
- loin knasion "zap", jonne siirsin ladatun <code>.sh</code> tiedoston.
- annoin suoritusoikeuden tiedostolle <code>chmod +x ZAP_2_14_0_unix.sh</code>
- käynnistin asennuksen <code>sudo ./ZAP_2_14_0_unix.sh</code>
- <code>standard installation</code>
- asennus valmis
  
<img src="/images/zap.png" alt="" title="" width="70%" height="70%">

- Firefoxin asetuksista laitoin HTTP Proxyksi: localhost ja portti 8080.

<img src="/images/fire1.png" alt="" title="" width="70%" height="70%">

- ZAP:ista Tools - Options - Dynamic SSL Certificate - Go To New Screen ja sieltä tallensin CA Certificaten työpöydälleni.
- Firefoxin asetuksista Settings- Privacy & Security - Certificates - View Certificates...- Import... ja lisäsin aikaisemmin tallettamani CA Certificaten.

<img src="/images/certi.png" alt="" title="" width="70%" height="70%">
<img src="/images/ca.png" alt="" title="" width="70%" height="70%">
  
- hakutulokset näkyivat ZAP:issa.

<img src="/images/zap2.png" alt="" title="" width="70%" height="70%">

## b) Kettumaista. Asenna FoxyProxy Standard Firefox Addon, ja lisää ZAP proxyksi siihen.

- latasin täältä: https://addons.mozilla.org/en-US/firefox/addon/foxyproxy-standard/

<img src="/images/foxy.png" alt="" title="" width="70%" height="70%">

- seuraavsta ponnahdusikkunasta hyväksyin seuraavat ehdot ja lisäsin laajennuksen

<img src="/images/add.png" alt="" title="" width="70%" height="70%">

- lisäsin proxyt HTTP- ja HTTPS-protokollalle

<img src="/images/foxy1.png" alt="" title="" width="70%" height="70%">
<img src="/images/foxy2.png" alt="" title="" width="70%" height="70%">

## PortSwigger Labs. Ratkaise tehtävät. Selitä ratkaisusi: mitä palvelimella tapahtuu, mitä eri osat tekevät, miten hyökkäys löytyi, mistä vika johtuu. (Voi käyttää ZAPia, vaikka malliratkaisut käyttävät harjoitusten tekijän maksullista ohjelmaa)
- Insecure Direct Object Reference (IDOR)
  - c) [Insecure direct object references](https://portswigger.net/web-security/access-control/lab-insecure-direct-object-references)
  Labrassa kerrotaan, että harjoitustehtävä tallentaa käyttäjien chat-lokit suoraan palvelimen tiedostojärjestelmään ja hakee ne staattisten URL-osoitteiden avulla. Ratkaise tehtävä löytämällä salasana käyttäjälle "carlos" ja kirjaudu sisään tilille.

Tehtävän aloitus on seuraavanlainen. Kuvauksessa annettiin vinkkiä chat-ominaisuudesta, joten lähdin tutkimaan sitä.

<img src="/images/aloitus.png" alt="" title="" width="70%" height="70%">

Testailin chat-ominaisuutta kirjoittamalla chattiin. Mielenkiintoisempi ominaisuus oli "View transcript"- nappi, joka lataa tekstitiedostona keskustelunne. Painamalla selaimess f12, se avaa meille kehittäjätyökalut. Seuratessa sivuston verkkoliikennettä lataamalla tiedosto, löydämme minne chatit tallennetaan.

<img src="/images/f12.png" alt="" title="" width="70%" height="70%">

Kopioin osoitteen, mutta muutankin osoitetta niin, että tiedoston nimeksi tulee "1.txt":

    https://0a85004003acbc2c818dcf5000e80052.web-security-academy.net/download-transcript/1.txt

Tällöin tietokoneelleni latautuu tiedosto, jossa on Carloksen keskustelu ja hän paljastaa siinä salasanansa.

<img src="/images/chat.png" alt="" title="" width="70%" height="70%">

Seuraavaksi suuntaamme "My account"-kohtaan ja kokeilemme toimiiko saamamme tunnukset.

<img src="/images/login.png" alt="" title="" width="70%" height="70%">

Pääsimme kirjautumaan tunnuksilla sisään.

<img src="/images/solved.png" alt="" title="" width="70%" height="70%">

Labra on ratkaistu.

- Path traversal
  - d) [File path traversal, simple case](https://portswigger.net/web-security/file-path-traversal/lab-simple)
    Tämä tehtävä sisältää polkuhakemistohaavoittuvuuden tuotteiden kuvien näyttämisessä. Tehtävä pitää ratkaista hakemalla tiedoston <code>/etc/passwd</code> sisältö.

<img src="/images/aloitus1.png" alt="" title="" width="70%" height="70%">

File path traversal liittyy verkkosovellusten ja palvelimien tietojen käsittelyyn ja haavoittuvuus mahdollistaa hyökkääjän siirtyä sovelluksen määrittelemän polun ulkopuolelle ja saada pääsy tiedostoihin tai hakemistoihin, joihin heillä ei pitäisi olla oikeuksia.

Lähdin tutkimaan mihin polkuun kuvat ovat tallennettu. Se selvisi äskeiseen tapaan käyttämällä selaimen kehittäjätyökaluja painamalla f12.

<img src="/images/image.png" alt="" title="" width="70%" height="70%">

Polku kuvaan on <code>	https://0a6e00b6043143718122025800c900e3.web-security-academy.net/image?filename=45.jpg</code>. 

Haluttu sisältö on polussa <code>/etc/passwd</code>, joten poistin URL:sta "45.jpg" ja lisäsin polun sen tilalle.

<img src="/images/nofile.png" alt="" title="" width="70%" height="70%">

Saamme ilmoituksen "No such file". Lisäämme polkuun <code>../</code>, sillä haluttu sisältö voi olla jossakin toisessa hakemistossa. Osoitteella <code>https://0a6e00b6043143718122025800c900e3.web-security-academy.net/image?filename=../../../etc/passwd</code> näkymä oli erilainen ja tämä kiinnitti huomioni:

<img src="/images/f12a.png" alt="" title="" width="70%" height="70%">

Poistin URL:sta sanan "image" ja labra saatiin suoritettua.

    https://0a6e00b6043143718122025800c900e3.web-security-academy.net/?filename=../../../etc/passwd

<img src="/images/solved2.png" alt="" title="" width="70%" height="70%">

    
  - e) [File path traversal, traversal sequences blocked with absolute path bypass](https://portswigger.net/web-security/file-path-traversal/lab-absolute-path-bypass)
Tehtävä on samankaltainen kuin edellinen. Tehtävässä sovellus estää polkuhakemiston sekvenssit, mutta käsittelee annettua tiedostonimeä suhteessa oletushakemistoon. Tehtävä ratkaistaan hakemalla tiedoston /etc/passwd sisältö.

Lähdin kokeilemaan samalla lähestymistavalla kuin aikasemman tehtävän: etsin kuvan plun, sen jälkeen laitoin kuvan tiedoston nimen tilalle <code>/etc/passwd</code>, jonka jälkeen sain samanlaisen näkymän kuin edellisessä tehtävässä.

    https://0a0f00f104aebb1184007974002d0097.web-security-academy.net/image?filename=/etc/passwd

<img src="/images/f12b.png" alt="" title="" width="70%" height="70%">

Poistin "image"-sanan ja labra oli suoritettu.

<img src="/images/solved3.png" alt="" title="" width="70%" height="70%">
    
  - f) [File path traversal, traversal sequences stripped non-recursively](https://portswigger.net/web-security/file-path-traversal/lab-sequences-stripped-non-recursively)
Harjoituksessa on sama kuvaus ja tehtävä kuin aikaisemmissa. Lähdin seuortittamaan testausta samalla tavalla kuin aikaisemmissa tehtävissä, mutta vaikuttaa siltä ettei hakemistoa löydy. Kokeilin hyödyntää <code>....//</code> URL:issa, jolloin ohitetaan filtteröinti. Lopulta päätteellä <code>/?filename=/....//....//....//etc/passwd</code> löysimme halutun hakemiston.

<img src="/images/solved4.png" alt="" title="" width="70%" height="70%">

- Server Side Template Injection (SSTI)
  - g) [Server-side template injection with information disclosure via user-supplied objects](https://portswigger.net/web-security/server-side-template-injection/exploiting/lab-server-side-template-injection-with-information-disclosure-via-user-supplied-objects) (Tämä on merkitty hieman vaikeammaksi, jätä viimeiseksi jos näyttää hankalalta)
- Server Side Request Forgery (SSRF)

  - h) [Basic SSRF against the local server](https://portswigger.net/web-security/ssrf/lab-basic-ssrf-against-localhost)
    
    Harjoitustehtävässä on varaston tarkistustoiminto, joka hakee tietoja sisäisestä järjestelmästä. Tehtävä tulee ratkaista vaihtamalla varaston tarkistuksen URL-osoite niin, että se kohdistaa admin-käyttöliittymään osoitteessa http://localhost/admin ja poista käyttäjän nimeltä "carlos".

Ensiksi suuntasin kohtaan "My account", josta löytyy kirjautumiskohta.
  
<img src="/images/login1.png" alt="" title="" width="70%" height="70%">

Kokeilin, jos pääkäyttäjälle pääsee lisäämällä URL:iin <code>/admin</code>, mutta sivua ei näytetty kirjautumatta.
<img src="/images/admin.png" alt="" title="" width="70%" height="70%">

Menin takaisin pääsivulle ja valitsin tuotteen. Tuotteen kohdalta löytyi "Check stock" nappi, joka tarkistaa tuotteen saatavuuden. Lisäsin <code>http://localhost/admin</code> osoitteen tilalle. Tällöin painamalla "Check stock" lähetän pyynnön määrittelemääni URL-osoitteeseen.

<img src="/images/stock.png" alt="" title="" width="70%" height="70%">

Pyyntö paljasti näytölle lisäinfoa, josta paljastuu käyttäjät "wiener" ja "carlos" sekä mahdollisuus poistaa käyttäjät.

<img src="/images/users.png" alt="" title="" width="70%" height="70%">

Nappi ei kuitenkaan poistanut käyttäjää, mutta saimme osoitteen, jota voimme hyödyntää: <code>http://localhost/admin/delete?username=carlos</code>. Lisäsin tämän osoitteen samaan kohtaan kuin edellisen, ja saimme poistettua käyttäjän "carlos".

<img src="/images/stock2.png" alt="" title="" width="70%" height="70%">
<img src="/images/solved5.png" alt="" title="" width="70%" height="70%">
Labra on suoritettu.

    
- Cross Site Scripting (XSS)
  - i) [Reflected XSS into HTML context with nothing encoded](https://portswigger.net/web-security/cross-site-scripting/reflected/lab-html-context-nothing-encoded)
    Harjoituksessa on reflected cross-site scripting- haavoittuvuus hakutoiminnassa. Tehtävä ratkaistaan suorittamalla cross-site scripting -hyökkäys, joka kutsuu alert-funktiota.

Hakutoiminto tulee vastaan kun labra aukeaa. Tehtävässä halutaan käyttää alert-funktiota, joten ajan hyökkäyksen antamalla kyselyn <code><script>alert(document.domain)</script></code>. 

<img src="/images/asd.png" alt="" title="" width="70%" height="70%">

Tällöin ilmestyy alert-ponnahduikkuna ja labra saadaan suoritettua. 

<img src="/images/alert.png" alt="" title="" width="70%" height="70%">
<img src="/images/solved6.png" alt="" title="" width="70%" height="70%">

Jos hakutoiminnassa on vastaavanlainen haavoittuvuus, hyökkääjä voi silloin suorittaa haluamaansa koodia.

  - j) [Stored XSS into HTML context with nothing encoded](https://portswigger.net/web-security/cross-site-scripting/stored/lab-html-context-nothing-encoded)
    Harjoituksessa on stored cross-site scripting-haavoittuvuus. Tehtävä ratkaistaan lähettämällä kommentti, joka kutsuu alert-funktiota, kun blogipostaus on nähty.

    Löysin kommenttiosion, jonne hyökkäys on tarkoitus tehdä. 

<img src="/images/comment.png" alt="" title="" width="70%" height="70%">

Lisäsin kommentiksi aikaisemman komennon <code<script>alert(document.domain)</script></code> ja saimme sillä tehtävän ratkaistua.
<img src="/images/asd1.png" alt="" title="" width="70%" height="70%">
<img src="/images/solved7.png" alt="" title="" width="70%" height="70%">


## k) Asenna Webgoat 2023.4. (Uusi versio, jossa on eri tehtäviä kuin vanhemmissa)
Lähdin suorittamaan asennusta Tero Karvisen [Try Web Hacking](https://terokarvinen.com/2023/webgoat-2023-4-ethical-web-hacking/) on New Webgoat 2023.4 ohjeen mukaisesti.

Asensin Javan ja palomuurin, ja laitoin sen myös päälle.

````
sudo apt-get update
sudo apt-get install openjdk-17-jre
sudo apt-get install ufw
sudo ufw enable
````
Latasin WebGoat 2023.4, sillä se on uusin versio.

    wget https://github.com/WebGoat/WebGoat/releases/download/v2023.4/webgoat-2023.4.jar

Koska meillä on ZAP proxy, vaihdetaan WebGoat porttiin 8888. 

    java -Dfile.encoding=UTF-8 -Dwebgoat.port=8888 -Dwebwolf.port=9090 -jar webgoat-2023.4.jar

Tämän jälkeen WebGoat käynnistyy ja ehdottaa menemään sivulle <code>http://127.0.0.1:8888/WebGoat</code>.
<img src="/images/wg.png" alt="" title="" width="70%" height="70%">

Loin käyttäjän.

## Ratkaise WebGoat 2023.4:
- m) (A1) Broken Access Control (WebGoat 2023.4)
  - Hijack a session (1)
 
    Kesken, jatkuu pian.

  - Insecure Direct Object References (4)
    IDOR ovat tietoturva-aukkoja, jossa sovellus ei asianmukaisesti valvo, kuka voi nähdä tai muokata tiettyjä objekteja tai resursseja. Hyökkääjä voi käyttää tätä aukkoa päästäkseen käsiksi tai muokatakseen tietoja, joihin hänellä ei pitäisi olla oikeuksia.

Tehtävässä pyydetään kirjautumaan sisään käyttämällä tunnuksia tom ja cat.
<img src="/images/tom.png" alt="" title="" width="70%" height="70%">
Kirjautumisen jälkeen näemme profiilin tiedoissa tiedot:
````
name:Tom Cat
color:yellow
size:small
````
<img src="/images/roleuserid2.png" alt="" title="" width="70%" height="70%">

Tehtävässä kysytään, että mitkä kaksi muuta attribuuttia löytyy vastauksesta. Tarkastelibn ZAP:lla pyyntöä ja sieltä löytyy kategoriat:
`````
"role" : 3,
"color" : "yellow",
"size" : "small,
"name" : "Tom Cat",
"userId" : "2342384"
`````
Eli uudet attribuutit olivat "role" ja "userID".

<img src="/images/roleuserid.png" alt="" title="" width="70%" height="70%">

Seuraavassa kohdassa kysytään vaihtoehtoista URL-osoitetta profiilin näyttämiseksi.

<img src="/images/guessing.png" alt="" title="" width="70%" height="70%">

Osoite profiiliin löytyi mysö ZAP:n avulla pyyntöä tutkiessa. Pyynnössä näkyvän linkin perään lisäämme käyttänä userID:n, ja linkki johtaa silloin käyttäjän profiiliin.

<img src="/images/profile.png" alt="" title="" width="70%" height="70%">

Seuraavaksi on tarkoitus katsoa toisen henkilö profiilia käyttämällä vaihtoehtoista polkua. Aikaisemmin käytimme vaihtoehtoista polkua
<code>http://127.0.0.1:8888/WebGoat/IDOR/profile/2342384</code>, joten syötämme tämän hakukenttään.

<img src="/images/4.png" alt="" title="" width="70%" height="70%">

Voimme löytää lisää käyttäjiiä userID:tä fuzzaamalla. Lähdin käsin kokeilemaan vaihtamalla viimisintä numeroa numerosarjassa ja userID:llä 2342388 sattui löytymään käyttäjä Buffalo Bill.

<img src="/images/8.png" alt="" title="" width="70%" height="70%">

Kopioin Buffalo Bill:n useID.n ja suunniistin takaisin WebGoatin tehtäväsivulle ja "View Prfofile"- napista painoin hiiren oikealla "inspect" ja muutin userID-kohdan linkistä saamaamme Bill:n usedID:ksi, jolloin saamme tehtävän suoritettua? Käytin samaa tekniikkaa myös käyttäjän muokkaus- kohdassa. En ole varma kuuluiko tehtävää suorittaa noin, sillä mitään tietoja en muokannut. Tämä pitää selvittää myöhemmin.

<img src="/images/f12c.png" alt="" title="" width="70%" height="70%">
<img src="/images/solved9.png" alt="" title="" width="70%" height="70%">

Apuna: https://www.youtube.com/watch?v=K5BBP88kBjU

  - Missing Function Level Access Control (3)
    
    Tehtävässä täytyy etsiä alla olevasta valikosta kaksi näkymätöntä valikkokohtaa, jotka kiinnostaisivat hyökkääjää, ja lähettää kohteiden tunnisteet.
    
    <img src="/images/menu1.png" alt="" title="" width="70%" height="70%">

    Aluksi katsoin sivun koodia, mutta en löytänyt mitään. Sitten painoin "Account"-painiketta ja katsoin ZAP:lla miltä näyttää.
    
    <img src="/images/zap1.png" alt="" title="" width="70%" height="70%">
    
    Siirryin selaimessa pyynnöstä löytyvään linkkiin ja sivulla näytti olevan samaa materiaalia kuin harjoituksen kuvauksessa, mutta ilman muotoiluja. Sivu on siis jotenkin rikki?
    
    <img src="/images/sivu.png" alt="" title="" width="70%" height="70%">

    Tutkin sivun koodia, ja sieltä löytyi kaksi piiloitettua valikkokohtaa <code>Users</code> ja <code>Config</code>.

    
    <img src="/images/sivu2.png" alt="" title="" width="70%" height="70%">
    <img src="/images/koodi.png" alt="" title="" width="70%" height="70%">

    Kirjoitin vastaukseni lomakkeeseen ja ne näyttäisivät olevan oikein.

    <img src="/images/menu2.png" alt="" title="" width="70%" height="70%">

    Seuraavassa vaiheessa, jossa täytyy löytää Jerryn hash, epäonnistuin. Kokeilin piilotettuja valikkoja ja tarkastelin ZAP:ia. En keksinyt ratkaisua.
    
    käytin apuna: https://www.youtube.com/watch?v=Kp-R25Zy7NI
    
  - Spoofing an Authentication Cookie (1)

Harjoitus käsittelee Authentication Cookieita. Nämä keksit ovat osa palveluita, jotka vaativat käyttäjän tunnistautumista. Kun käyttäjä kirjautuu palveluun henkilökohtaisella käyttäjänimellä ja salasanalla, palvelin tarkistaa annetut tunnistetiedot ja jos ne ovat oikeita, palvelin luo istunnon. Jokaisella istunnolla on uniikki tunniste.

Tehtävän tavoitteena on ohittaa todennusmekanismi väärentämällä keksi.

Kirjauduin ensin sisälle käyttämällä tunnuksia admin:admin ja webgoat:webgoat. ZAP:illa löysin keksin ja tallensin sen muistioon.

<img src="/images/admin2.png" alt="" title="" width="70%" height="70%">

Keksit näköjään myös näkyivät käyttöliittymässä, joten tallensin myös webgoat-käyttäjän keksin muistioon.

<img src="/images/goat.png" alt="" title="" width="70%" height="70%">

Molemmat keksit ovat Base64-koodattu. Sen tunnistaa siitä, esim. että hash-funktio loppuu "=" ja sisältää pieniä- ja isojakirjaimia sekä numeroita ja erikoismerkkejä kuten / ja +. Käytin sivustoa https://www.base64decode.org/ dekoodaamiseen.  

<img src="/images/base64.png" alt="" title="" width="70%" height="70%">

Saimme hexakoodia, ja sen dekoodaamiseen käytin sivustoa https://cryptii.com/pipes/hex-to-text. 

<img src="/images/hexa.png" alt="" title="" width="70%" height="70%">

Käytin molempiin kekseihin samaa teksniikkaa. Sain seuraavat tulokset:

`````
admin:
NGQ0ODZmNzg3NTUyNjQ2YTYzNTI2ZTY5NmQ2NDYx
MHoxuRdjcRnimda

webgoat:
NGQ0ODZmNzg3NTUyNjQ2YTYzNTI3NDYxNmY2NzYyNjU3Nw== webgoat
MHoxuRdjcRtaogbew
`````
Kun katsoo dekoodattuja koodeja, voimme huomata, että molempien käyttäjien sarjojen lopussa on käyttäjä nimi väärinpäin: nimda- > admin. Alussa kuitenkin molemmissa sama kirjainsarja. Tästä voi päätellä, että tehtävässä haettu Tomin keksi voisi mahdollisesti olla <code>MHoxuRdjcRmot</code> ja samalla tekniikalla enkoodattuna eli <code>NGQ0ODZmNzg3NTUyNjQ2YTYzNTI2ZDZmNzQ=</code>.

ZAP:issa hain pyynnön, jossa olin kirjautunut sisälle. Avasin sen requester tabissa ja muutin keksiksi olettamani Tomin keksin ja lähetin kutsun. 

<img src="/images/request.png" alt="" title="" width="70%" height="70%">

Huomasinkin, että tehtävä oli suoritettu.

<img src="/images/solved10.png" alt="" title="" width="70%" height="70%">

Apuna: https://www.youtube.com/watch?v=-n4OmhUN3vA
    
- n) (A7) Identity & Auth Failure (WebGoat 2023.4)
  - Authentication Bypasses (1)
Autentikoinnin ohitus hyödyntää yleensä joitakin virheitä kokoonpanossa tai logiikassa.

Harjoituksessa harjoitellaan autentikoinnin ohitusta tilanteessa, jossa olen nollannut salasanani. Mnun on vastattava turvakysymyksiin, mutta en muista vastauksia.

<img src="/images/skenaario.png" alt="" title="" width="70%" height="70%">

Aluksi kirjoitin kysymyksine kohdalle "test" ja katsoin ZAP:ista miltä näyttää. POST- pyynnön kohdalla valitsin "open in requester tab" ja poistin kohdat <code>secQuestion0=test&secQuestion1=test</code> ja lähetin pyynnön, mutta ei toiminut. 

<img src="/images/sec2.png" alt="" title="" width="70%" height="70%">

Poistin myös kohdan <code>verifyMethod=SEC_QUESTIONS</code>, sekään ei toiminut. Tehtävä ratkesi sillä, että muutti parametrit <code>secQuestion111=test&secQuestion000=test</code>.

<img src="/images/sec2.png" alt="" title="" width="70%" height="70%">

Apuna: https://www.youtube.com/watch?v=4mv4qfUHaDg

  - Insecure Login (1)

Salakirjoitus on olennainen työkalu turvallisen viestinnän varmistamiseksi. Tässä harjoituksessa tulee käyttää snifferiä kirjautumistietojen kaappaamiseen.

<img src="/images/sec2.png" alt="" title="" width="70%" height="70%">

Ensiksi painoin "Log In"- nappia ja tarkistin POST-pyynnön ZAP:issa. 

<img src="/images/zap5.png" alt="" title="" width="70%" height="70%">

ZAP paljastaa käyttäjätunnuksen <code>CaptainJack:BlackPearl</code>. Kokeilin tunnuksia sisäänkirjautumiseen ja pääsin kirjautumaan.

<img src="/images/solved11.png" alt="" title="" width="70%" height="70%">

- o) (A10) Server-side Request Forgery (WebGoat 2023.4)
  - Server-Side Request Forgery (2)

    Server-Side Request Forgery -hyökkäyksessä hyökkääjä voi väärinkäyttää palvelimen toiminnallisuutta ja lukea tai päivittää sisäisiä resursseja. Hyökkääjä voi syöttää tai muokata URL-osoitetta, jonka        koodi palvelimella lukee tai lähettää tietoja hyökkääjälle.

Tehtävässä täytyy saada palvelin jotakin muuta kuin pitäisi.
    
<img src="/images/alku12.png" alt="" title="" width="70%" height="70%">

Painoin nappia "Steal the cheese" ja sain seuraavan kuvan:

<img src="/images/tom1.png" alt="" title="" width="70%" height="70%">

Painoin napin kohdalla hiiren oikeaa ja "inspect". Päätin ensimmäiseksi kokeilla muuttaa tom.png -> cheese.png

<img src="/images/f12d.png" alt="" title="" width="70%" height="70%">

Ja sainkin yllätyksekseni toisen kuvan.

<img src="/images/plan2.png" alt="" title="" width="70%" height="70%">

Ilmeisesti tämä ei kuitenkaan tarkoita, että tehtävä on läpäisty. Sama näkymä tulee vaikka kuvan nimeksi antaisi jotain muuta. Kun vaihdoin tom.png -> jerry.png, saatiin tehtävä ratkaistua.

<img src="/images/jerry.png" alt="" title="" width="70%" height="70%">
    
Seuraava tehtävä on samankaltainen. 

<img src="/images/alku3.png" alt="" title="" width="70%" height="70%">

Kun painaa nappia, näkymä on tällainen:

<img src="/images/alku4.png" alt="" title="" width="70%" height="70%">

Lähdin etenemään samalla tavalla kuin edellisessä tehtävässä. Vaihdoin kuvan osoitteen tehtävänannossa mainittuun osoitteeseen ja tehtävä saatiin ratkaistua.

<img src="/images/f12e.png" alt="" title="" width="70%" height="70%">
<img src="/images/solved13.png" alt="" title="" width="70%" height="70%">
    
- p) Client side (WebGoat 2023.4)
  - Bypass front-end restrictions (2)
 
    Bypass front-end restrictions tarkoittaa yrittämistä ohittaa web-sovelluksen rajoitukset, kuten tiettyjen syötteiden estäminen tai salliminen. Hyökkääjä pyrkii manipuloimaan selaimen lähettämiä pyyntöjä voidakseen antaa tietynlaisia syötteitä, jotka muutoin olisivat estettyjä.

    <img src="/images/alku5.png" alt="" title="" width="70%" height="70%">

    Kokeilin ensin vaihtaa esim. option1 -> option3, 5 merkin merkkiraja -> 10 merkin raja. Tämä ei toiminut. Tajusin, että "value"-arvoa täytyy muuttaa, joten lisäsin merkin "x" kaikkien laatikoiden value-kohtaan.

<img src="/images/value.png" alt="" title="" width="70%" height="70%">

Se tepsi lomakkeeseen.

<img src="/images/solved14.png" alt="" title="" width="70%" height="70%">

Lähteet:

Karvinen, Tero: Tunkeutumistestaus, h4-Totally Legit Sertificate (https://terokarvinen.com/2023/eettinen-hakkerointi-2023/#h4-totally-legit-sertificate)

Jezztom, Hijacking a session webgoat solution, YouTube, 2023 (https://www.youtube.com/watch?v=htZCniUCoa4)

Karvinen Tero, Tunkeutumistestaus, Try Web Hacking on New Webgoat 2023.4 (https://terokarvinen.com/2023/webgoat-2023-4-ethical-web-hacking/)

Chat GPT (chat.openai.com)

Jana Halt, h4 Totally Legit Sertificate, Github, 2023 (https://github.com/JanaHalt/Ethical-Hacking-2023/blob/main/h4%20Totally%20Legit%20Sertificate.md)

PseudoTime, WebGoat 8 - Insecure Direct Object References - Playing with the Patterns, YouTube, 2021 (https://www.youtube.com/watch?v=K5BBP88kBjU)

Max Integrations, [A1] Spoofing an Authentication Cookie - WebGoat, YouTube, 2022 (https://www.youtube.com/watch?v=-n4OmhUN3vA)

base64encode, base64 (https://www.base64encode.org/)

Cryptii, hex to text (https://cryptii.com/pipes/hex-to-text)

Hacking0x101, Web Goat Auth Bypass, 2021, (https://www.youtube.com/watch?v=4mv4qfUHaDg)





