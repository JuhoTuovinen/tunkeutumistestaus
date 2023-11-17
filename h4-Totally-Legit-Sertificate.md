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
        - Puhdista ja validoi kaikki käyttäjän antamat syötteet
        - älä lähetä raakoja vastauksia asiakkaille
        - poista HTTP-uudelleenohjaukset käytöstä
        - älä käytä estoluetteloita, ne voidaan ohittaa
    - Esmierkki skenaario: hyökkääjät voivat päästä käsiksi paikallisiin tiedostoihin tai sisäisiin palveluihin saadakseen arkaluonteista tietoa, kuten file:///etc/passwd ja http://localhost:28017/

- PortSwigget Academy:
  - [Access control vulnerabilities and privilege escalation](https://portswigger.net/web-security/access-control) (IDOR on osa tätä)
  - [Server-side template injection](https://portswigger.net/web-security/server-side-template-injection)https://portswigger.net/web-security/server-side-template-injection
  - [Server-side request forgery (SSRF)](https://portswigger.net/web-security/ssrf)https://portswigger.net/web-security/ssrf
  - [Cross-site scripting](https://portswigger.net/web-security/cross-site-scripting)https://portswigger.net/web-security/cross-site-scripting
 
- Karvinen 2020: [Using New Webgoat 2023.4 to Try Web Hacking](https://terokarvinen.com/2023/webgoat-2023-4-ethical-web-hacking/)

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
  
- ZAP:issa ajoin käyttämällä localhost:8080

<img src="/images/zap2.png" alt="" title="" width="70%" height="70%">


apuna: https://www.youtube.com/watch?v=d6dOGlzpz8w 
## b) Kettumaista. Asenna FoxyProxy Standard Firefox Addon, ja lisää ZAP proxyksi siihen.
## PortSwigger Labs. Ratkaise tehtävät. Selitä ratkaisusi: mitä palvelimella tapahtuu, mitä eri osat tekevät, miten hyökkäys löytyi, mistä vika johtuu. (Voi käyttää ZAPia, vaikka malliratkaisut käyttävät harjoitusten tekijän maksullista ohjelmaa)
- Insecure Direct Object Reference (IDOR)
  - c) [Insecure direct object references](https://portswigger.net/web-security/access-control/lab-insecure-direct-object-references)
   
    
- Path traversal
  - d) [File path traversal, simple case](https://portswigger.net/web-security/file-path-traversal/lab-simple)
  - e) [File path traversal, traversal sequences blocked with absolute path bypass](https://portswigger.net/web-security/file-path-traversal/lab-absolute-path-bypass)
  - f) [File path traversal, traversal sequences stripped non-recursively](https://portswigger.net/web-security/file-path-traversal/lab-sequences-stripped-non-recursively)
- Server Side Template Injection (SSTI)
  - g) [Server-side template injection with information disclosure via user-supplied objects](https://portswigger.net/web-security/server-side-template-injection/exploiting/lab-server-side-template-injection-with-information-disclosure-via-user-supplied-objects) (Tämä on merkitty hieman vaikeammaksi, jätä viimeiseksi jos näyttää hankalalta)
- Server Side Request Forgery (SSRF)
  - h) [Basic SSRF against the local server](https://portswigger.net/web-security/ssrf/lab-basic-ssrf-against-localhost)
- Cross Site Scripting (XSS)
  - i) [Reflected XSS into HTML context with nothing encoded](https://portswigger.net/web-security/cross-site-scripting/reflected/lab-html-context-nothing-encoded)
  - j) [Stored XSS into HTML context with nothing encoded](https://portswigger.net/web-security/cross-site-scripting/stored/lab-html-context-nothing-encoded)

## k) Asenna Webgoat 2023.4. (Uusi versio, jossa on eri tehtäviä kuin vanhemmissa)
## Ratkaise WebGoat 2023.4:
- m) (A1) Broken Access Control (WebGoat 2023.4)
  - Hijack a session (1)
  - Insecure Direct Object References (4)
  - Missing Function Level Access Control (3)
  - Spoofing an Authentication Cookie (1)
- n) (A7) Identity & Auth Failure (WebGoat 2023.4)
  - Authentication Bypasses (1)
  - Insecure Login (1)
- o) (A10) Server-side Request Forgery (WebGoat 2023.4)
  - Server-Side Request Forgery (2)
- p) Client side (WebGoat 2023.4)
  - Bypass front-end restrictions (2)
