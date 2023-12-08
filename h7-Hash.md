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
- Karvinen 2023: [Crack File Password With John](https://terokarvinen.com/2023/crack-file-password-with-john/)



## a) Hashcat. Asenna Hashcat ja testaa sen toimivuus ratkaisemalla tiiviste.
- asennus
sudo apt update
sudo apt install hashcat

$ hashcat --version
v6.2.6

- tiedostn luonti

echo "Salainen sisältö." > salainen_tiedosto.txt


apuna: https://www.youtube.com/watch?v=fVgzY5OJeIE ja https://www.md5hashgenerator.com/

<img src="/images/cat1.png" alt="" title="" width="70%" height="70%">


- kopioin md5 hash
- testaan hash identifier tunnistaako "hash-identifier"

``````
HASH: 0cd698a0503946a852f2f81cc7d63ee3

Possible Hashs:
[+] MD5
[+] Domain Cached Credentials - MD4(MD4(($pass)).(strtolower($username)))
``````
tunnisti että hash on MD5

- tallennan hashin tiedostoon "echo "0cd698a0503946a852f2f81cc7d63ee3" > hash.txt"
- pitää tietää mitää modulia käytetään

`````
┌──(kali㉿kali)-[~/Desktop]
└─$ hashcat -h |grep MD5                                                              
      0 | MD5                                                        | Raw Hash
   5100 | Half MD5                                                   | Raw Hash
     50 | HMAC-MD5 (key = $pass)                                     | Raw Hash authenticated
     60 | HMAC-MD5 (key = $salt)                                     | Raw Hash authenticated
  11900 | PBKDF2-HMAC-MD5                                            | Generic KDF
  11400 | SIP digest authentication (MD5)                            | Network Protocol
   5300 | IKE-PSK MD5                                                | Network Protocol
    ....(JATKUU)....
`````

- tässä tapauksessa module 0 koska raw hash
- aloitetaan kräkkääminen "hashcat -m 0 hash.txt /usr/share/wordlists/rockyou.txt"
- kräkätty
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
- ensimmäiseltä riviltä nähdään hash ja salasana, jonka muutin MD5 Hashiksi.

## b) John. Asenna Jumbo John ja testaa sen toimivuus murtamalla jonkin tiedoston salasana.


- asennan työpöydälle git clone https://github.com/openwall/john.git
- luon aikaisemmin luodusta hahs.txt- tiedostosta zip tiedoston joka aukeaa salasananalla "kali123": zip --password kali123 hash.zip hash.txt 

<img src="/images/zip1.png" alt="" title="" width="70%" height="70%">

- tarvitsemme password hashin, käytetään zip2john

``````
$ zip2john hash.zip
ver 1.0 efh 5455 efh 7875 hash.zip/hash.txt PKZIP Encr: 2b chk, TS_chk, cmplen=45, decmplen=33, crc=22B5A86E ts=75A2 cs=75a2 type=0
hash.zip/hash.txt:$pkzip$1*2*2*0*2d*21*22b5a86e*0*42*0*2d*75a2*9be3a7a90bc22865b4132e73371ee66c96797fe16cf103398412fc302c188ca026e9c8b3d0a5b5befb9300f206*$/pkzip$:hash.txt:hash.zip::hash.zip
``````
- tallenetaa hash tiedostoon "zip2john hash.zip > hash2.txt". Annoin uuden tiedoston nimeksi hash2.
- aloitetaan kräkkäys

``````
$ john --format=zip hash2.txt                                            
Using default input encoding: UTF-8
No password hashes loaded (see FAQ)
``````

ei onnistunut

- siirtin hash2.txt tiedoston cp ~/Desktop/hash2.txt ~/Desktop/john/run ja ajoin sillä john hash2.txt. nyt onnistui


<img src="/images/john1.png" alt="" title="" width="70%" height="70%">


## c) f5bc7fcc7f5b3b6af7ff79e0feafad6d1a948b6a2c18de414993c1226be48c1f on erään tällä tehtäväsivulla olevan yksittäisen sanan tiiviste. Käytin hyvin yleistä ja tunnettua tiivistealgoritmia. Sanassa voi olla isoja kirjaimia, mutta ei erikoismerkkejä. Minkä sanan tiiviste on kyseessä?

Hash identifier kertoo:

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
tallennetaan tiedostoon

    echo "f5bc7fcc7f5b3b6af7ff79e0feafad6d1a948b6a2c18de414993c1226be48c1f" > hash3.txt            

kokeillaan lähteekö seuraavalla komennolla: hashcat -m 1470 hash3.txt /usr/share/wordlists/rockyou.txt

- en saanut kuitenkaan hashia murrettua hashcatilla.
- päätin lähteä yrittämään internetistä löytyviä sha-256 dekoodereita ja törmäsin https://md5decrypt.net/en/Sha256/, jolla dekryptaus onnistuikin. Sanaksi paljastui "Sertificate". 
<img src="/images/sha256.png" alt="" title="" width="70%" height="70%">

Sana myös lukee sivulla niin kuin tehtävän annossa kerrotaan. Eli tiiviste suurella todennäköisyydella on SHA-256.


<img src="/images/serti.png" alt="" title="" width="70%" height="70%">


## d) Cheatsheet. Kerää kurssilaisten raporteista käteviä tekniikoita. Kerää itse tekniikat ja komennot, älä pelkästään kuvaile. Muista lähdeviitteet. Tee tiivis ja selkeä cheatsheet, josta löydät tarvittavat tiedot lipunryöstössä. (Tässä alatehtävässä ei tarvitse tehdä testejä koneella)






## e) Viittaa. Tarkista, että jokaisessa raportissasi on lähdeviitteet kunnossa. Jokaisen raportin tulee viitata ainakin kurssiin / tehtäväsivuun. Kaikkiin muihinkin käytettyihin lähteisiin tulee viitata, kuten kurssikavereiden raportteihin, weppisivuihin, man-sivuihin... (Tässä alatehtävässä ei tarvitse tehdä testejä koneella).




## Lähteet

https://terokarvinen.com/2023/eettinen-hakkerointi-2023/#h6-attaaack

https://www.youtube.com/watch?v=fVgzY5OJeIE

https://www.md5hashgenerator.com/
