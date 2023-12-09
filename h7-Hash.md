# Hash
Juho Tuovinen

Tässä raportissa aioin kertoa kuinka suoritin tehtävät, jotka on annettu Haaga Helian Tunkeutumisteustaus kurssilla. Tehtävät löytyvät [täältä](https://terokarvinen.com/2023/eettinen-hakkerointi-2023/#h7-hash).

Tietokoneen speksit, jolla suoritan toimenpiteen:
- Apple MacBook 2015
- macOs Monterey versio 12.7
- Ram -muistia 16GB
- levytilaa/muistia vapaana 35,88 GB.

Ohjelmistot
- UTM Version 4.1.6 (75)
- VM OS: Kali Linux 2023.3 (kali-rolling)


## x) Lue/katso/kuuntele ja tiivistä. (Tässä x-alakohdassa ei tarvitse tehdä testejä tietokoneella, vain lukeminen tai kuunteleminen ja tiivistelmä riittää. Tiivistämiseen riittää muutama ranskalainen viiva.)
- Karvinen 2022: [Cracking Passwords with Hashcat](https://terokarvinen.com/2022/cracking-passwords-with-hashcat/)
  - Järjestelmät eivät tallenna alkuperäisiä salasanoja, vaan hasheja. Hashia ei voi muuttaa takaisin salasanaksi, mutta voit laittaa tietokoneen kokeilemaan kaikkia sanakirjan sanoja ja kertomaan, jos jokin niistä täsmää.
  - Asenna Hashcat <code>sudo apt-get update && sudo apt-get -y install hashid hashcat wget</code>
  - luo kansio projektille
  - asenna rockyou.txt <code>wget https://github.com/danielmiessler/SecLists/raw/master/Passwords/Leaked-Databases/rockyou.txt.tar.gz</code>
  - Hashcat tarvitsee hash-tyypin ratkaistakseen hashin, joten hashin tyyppi täytyy ensin tunistaa
  - kräkätään hash komennolla <code>hashcat -m 0 '6b1628b016dff46e6fa35684be6acc96' rockyou.txt -o solved</code>
  - nopeampi suorittaa koneen isäntäkoneen käyttöjärjestelmässä kuin virtuaalikoneessa

- Karvinen 2023: [Crack File Password With John](https://terokarvinen.com/2023/crack-file-password-with-john/)
  - Monet tiedostomuodot tukevat salasanasalausta. John the Ripper voi murtaa nämä salasanat sanakirjahyökkäyksellä. Tässä artikkelissa opetetaan hankkimaan Jumbo-versio ja kääntämään se sekä testataan salasanan murtamista testiympäristössä.
  - John the ripperin asennus: <code>git clone --depth=1 https://github.com/openwall/john.git</code>
  - Suoritettavat tiedostot ja skriptit löytyvät run/-kansiosta
  - harjoituksessa käytettävä [zip-tiedosto](https://terokarvinen.com/2023/crack-file-password-with-john/tero.zip)
  - aloitetaan murtaminen muuttamalla ZIP- tiedsoton salasana hashiksi <code>$HOME/john/run/zip2john tero.zip >tero.zip.hash</code>
  - murretaan hash rockyou-sanakirjalla <code>$HOME/john/run/john tero.zip.hash </code>
  - John the ripper pystyy murtamaan muitakin kohteita


## a) Hashcat. Asenna Hashcat ja testaa sen toimivuus ratkaisemalla tiiviste.

Aloitin asennuksen päivittämällä pakentinhallintajärjestelmän ja sen jälkeen asensin Hashcatin paketinhallinnasta.
`````
sudo apt update
sudo apt install hashcat
`````
Tarkistin vielä että se on asentunu ja samalla tarkistin version.
````
$ hashcat --version
v6.2.6
````

Käytin apuna [How To Crack Hashes Using Hashcat](https://www.youtube.com/watch?v=fVgzY5OJeIE) -videota Hashcatin käyttöön ja hashin murtamiseen. Hashin luomiseen käytin verkkosivustoa https://www.md5hashgenerator.com/.

Loin MD5 hashin. Salasanani on "kali123", josta on luotu hash.

<img src="/images/cat1.png" alt="" title="" width="70%" height="70%">

Testaan tunistaako Hash Identifier hashia. Avaan komennolla <code>hash-identifier</code> ja syötän hashin.


``````
HASH: 0cd698a0503946a852f2f81cc7d63ee3

Possible Hashs:
[+] MD5
[+] Domain Cached Credentials - MD4(MD4(($pass)).(strtolower($username)))
``````
Identifier tunnisti, että hash on mahdollisesti tyyppiä MD5.

Tallennan hashin tiedostoon murtamista varten <code>echo "0cd698a0503946a852f2f81cc7d63ee3" > hash.txt"</code>. Tarkistan mitä modulia käytetään, jotta Hashcat osaa alkaa murtaman hashia.

`````
$ hashcat -h |grep MD5                                                              
      0 | MD5                                                        | Raw Hash
   5100 | Half MD5                                                   | Raw Hash
     50 | HMAC-MD5 (key = $pass)                                     | Raw Hash authenticated
     60 | HMAC-MD5 (key = $salt)                                     | Raw Hash authenticated
  11900 | PBKDF2-HMAC-MD5                                            | Generic KDF
  11400 | SIP digest authentication (MD5)                            | Network Protocol
   5300 | IKE-PSK MD5                                                | Network Protocol
    ....(JATKUU)....
`````

Tässä tapauksessa module "0", koska tarkaisemme pelkän hashin. Aloitetaan murtaminen <code>hashcat -m 0 hash.txt /usr/share/wordlists/rockyou.txt"</code>.
- <code>-m 0</code>: moduli 0
- <code>hash.txt</code>: tiedosto, johon olen tallentanut luomani MD5-hashin
- <code>/usr/share/wordlists/rockyou.txt</code>: kansio, jossa sanakirja sijaitsee. Käytän tässä rockyou.txt- sanakirjaa apuna.

Tulos:
``````
0cd698a0503946a852f2f81cc7d63ee3:kali123                  
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 0 (MD5)
Hash.Target......: 0cd698a0503946a852f2f81cc7d63ee3
Time.Started.....: Fri Dec  8 15:00:50 2023 (1 sec)
Time.Estimated...: Fri Dec  8 15:00:51 2023 (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:   328.5 kH/s (0.13ms) @ Accel:256 Loops:1 Thr:1 Vec:4
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 78848/14344385 (0.55%)
Rejected.........: 0/78848 (0.00%)
Restore.Point....: 77824/14344385 (0.54%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidate.Engine.: Device Generator
Candidates.#1....: superm -> jasons1

Started: Fri Dec  8 15:00:05 2023
Stopped: Fri Dec  8 15:00:52 2023
``````
Ensimmäiseltä riviltä nähdään hash ja salasana, jonka muutin alussa MD5-hashiksi, eli murtaminen onnistui.

## b) John. Asenna Jumbo John ja testaa sen toimivuus murtamalla jonkin tiedoston salasana.

Asennan John the ripper -github varaston työpöydälle <code>git clone https://github.com/openwall/john.git</code>. Luon aikaisemmin luodusta hash.txt- tiedostosta ZIP- tiedoston, joka aukeaa salasananalla "kali123".

    zip --password kali123 hash.zip hash.txt 

<img src="/images/zip1.png" alt="" title="" width="70%" height="70%">

Tarvitsemme salasanasta hashin; käytetään <code>zip2john</code>

``````
$ zip2john hash.zip
ver 1.0 efh 5455 efh 7875 hash.zip/hash.txt PKZIP Encr: 2b chk, TS_chk, cmplen=45, decmplen=33, crc=22B5A86E ts=75A2 cs=75a2 type=0
hash.zip/hash.txt:$pkzip$1*2*2*0*2d*21*22b5a86e*0*42*0*2d*75a2*9be3a7a90bc22865b4132e73371ee66c96797fe16cf103398412fc302c188ca026e9c8b3d0a5b5befb9300f206*$/pkzip$:hash.txt:hash.zip::hash.zip
``````
Tallenetaa hash uuteen tiedostoon hash2.txt: <code>zip2john hash.zip > hash2.txt"</code>. Siten aloitetaan murtaminen

``````
$ john --format=zip hash2.txt                                            
Using default input encoding: UTF-8
No password hashes loaded (see FAQ)
``````

Ei onnistunut.

Katsoin apua aikaisemmasta [Tero Karvisen artikkelista](https://terokarvinen.com/2023/crack-file-password-with-john/) ja siirtin hash2.txt tiedoston työpöydältä run-kansioon ja ajoin siellä. 

    john hash2.txt.

Nyt onnistui.

<img src="/images/john1.png" alt="" title="" width="70%" height="70%">

## c) f5bc7fcc7f5b3b6af7ff79e0feafad6d1a948b6a2c18de414993c1226be48c1f on erään tällä tehtäväsivulla olevan yksittäisen sanan tiiviste. Käytin hyvin yleistä ja tunnettua tiivistealgoritmia. Sanassa voi olla isoja kirjaimia, mutta ei erikoismerkkejä. Minkä sanan tiiviste on kyseessä?

Kokeilin ensin Hash Identifier:ia:

``````
 HASH: f5bc7fcc7f5b3b6af7ff79e0feafad6d1a948b6a2c18de414993c1226be48c1f

Possible Hashs:
[+] SHA-256
[+] Haval-256
```````
```````
$ hashcat -h |grep sha    
    170 | sha1(utf16le($pass))                                       | Raw Hash
   1470 | sha256(utf16le($pass))                                     | Raw Hash
  10870 | sha384(utf16le($pass))                                     | Raw Hash
   1770 | sha512(utf16le($pass))                                     | Raw Hash
  21300 | md5($salt.sha1($salt.$pass))                               | Raw Hash salted and/or itera
```````
Tallennetaan has tiedostoon hash3.txt. Koska tehtävänannossa kerrottiin "Käytin hyvin yleistä ja tunnettua tiivistealgoritmia", oletan, että kyseessä voisi olla SHA-256.

    echo "f5bc7fcc7f5b3b6af7ff79e0feafad6d1a948b6a2c18de414993c1226be48c1f" > hash3.txt            

Kokeillaan lähteekö seuraavalla komennolla <code>hashcat -m 1470 hash3.txt /usr/share/wordlists/rockyou.txt</code>. "Exhausted" eli en saanut hashia murrettua Hashcatilla. Sanalistan sanat eivät täsmännee tiivisteen kanssa. Päätin lähteä yrittämään internetistä löytyviä sha-256 dekoodereita ja törmäsin https://md5decrypt.net/en/Sha256/, jolla dekryptaus yllätyksekseni onnistuikin. Sanaksi paljastui "Sertificate". 

<img src="/images/sah256.png" alt="" title="" width="70%" height="70%">

Sana myös klytyy sivulta, niin kuin tehtävänannossa kerrotaan. Eli tiiviste suurella todennäköisyydella on tyyppiä SHA-256.

<img src="/images/serti.png" alt="" title="" width="70%" height="70%">

## d) Cheatsheet. Kerää kurssilaisten raporteista käteviä tekniikoita. Kerää itse tekniikat ja komennot, älä pelkästään kuvaile. Muista lähdeviitteet. Tee tiivis ja selkeä cheatsheet, josta löydät tarvittavat tiedot lipunryöstössä. (Tässä alatehtävässä ei tarvitse tehdä testejä koneella)

### Fuzzaus
### Porttiskannaus
   - <code>sudo nmap -p- -sV [IP]</code> skannaa portit 0-65535, <code>-sV<code> on palvelu- ja versiotiedot hakeva porttiskannaus ([Sawulohi](https://github.com/sawulohi/PenTest/tree/main/h6)).
   - <code>sudo nmap [IP] -p- -A<code> skannaa kaikki portit ja suorittaa käyttöjärjestelmän tunnistuksen ja version tunnistuksen ([Sawulohi](https://github.com/sawulohi/PenTest/blob/main/h1/README.md)).




## e) Viittaa. Tarkista, että jokaisessa raportissasi on lähdeviitteet kunnossa. Jokaisen raportin tulee viitata ainakin kurssiin / tehtäväsivuun. Kaikkiin muihinkin käytettyihin lähteisiin tulee viitata, kuten kurssikavereiden raportteihin, weppisivuihin, man-sivuihin... (Tässä alatehtävässä ei tarvitse tehdä testejä koneella).




## Lähteet

https://terokarvinen.com/2023/eettinen-hakkerointi-2023/#h7-hash

https://terokarvinen.com/2022/cracking-passwords-with-hashcat/

https://terokarvinen.com/2023/crack-file-password-with-john/

https://www.youtube.com/watch?v=fVgzY5OJeIE

https://www.md5hashgenerator.com/

https://md5decrypt.net/en/Sha256/

https://www.dcode.fr/sha256-hash

https://www.youtube.com/watch?v=TfmY03B07ww

https://github.com/openwall/john

https://md5hashing.net/hash

https://www.youtube.com/watch?v=XjVYl1Ts6XI&t=383s

https://chat.openai.com/
