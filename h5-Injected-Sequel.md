# Injected Sequel
Juho Tuovinen

Tässä raportissa aioin kertoa kuinka suoritin tehtävät, jotka on annettu Haaga Helian Tunkeutumisteustaus kurssilla. Tehtävät löytyvät [täältä](https://terokarvinen.com/2023/eettinen-hakkerointi-2023/#h5-injected-sequel).

Tietokoneen speksit, jolla suoritan toimenpiteen:
- Apple MacBook 2015
- macOs Monterey versio 12.7
- Ram -muistia 16GB
- levytilaa/muistia vapaana 11,96 GB.

Ohjelmistot
- UTM Version 4.1.6 (75)
- VM OS: Kali Linux 2023.3 (kali-rolling)

## x) Lue/katso/kuuntele ja tiivistä. (Tässä x-alakohdassa ei tarvitse tehdä testejä tietokoneella, vain lukeminen tai kuunteleminen ja tiivistelmä riittää. Tiivistämiseen riittää muutama ranskalainen viiva.)

- Karvinen 2016: [PostgreSQL Install and One Table Database – SQL CRUD tutorial for Ubuntu](https://terokarvinen.com/2016/03/05/postgresql-install-and-one-table-database-sql-crud-tutorial-for-ubuntu/) (Virtuaaliympäristöä ei tarvita, voit aloittaa kohdasta "Three line install"
 
  - artikkelissa käsitellään PostgreSQL- tietokannan asennus
  - PostgreSQL on suosittu tietokanta, jota käytetään usein yhdessä Python Djangon, Python Flaskin ja monien muiden frameworkien kanssa
  - Asennus:
    `````
    $ sudo apt-get update
    $ sudo apt-get -y install postgresql
    $ sudo systemctl start postgresql # needed in 2023
    $ sudo -u postgres createdb $(whoami)
    $ sudo -u postgres createuser $(whoami)
    `````
  - kun käyttää Linux-järjestelmän käyttäjänimeäsi PostgreSQL-tietokannalle ja PostgreSQL-käyttäjälle, tunnistautuminen tapahtuu automaattisesti
  - "help"- komennolla saadaan apua
  - PostgreSQLn sisäiset komennot käyttävät kauttaviivaa "\" ja joitakin kirjaimia. Komentoapu on kauttaviiva - kysymysmerkki (/?)
  - CRUD: create, read, update, delete
  - tauluja luodessa jokaiselle taululle tulisi asettaa SERIAL id-kenttä pääavaimeksi
  - Esimerkkiomentoja:
    - CREATE TABLE - <code>CREATE TABLE students (id SERIAL PRIMARY KEY, name VARCHAR(200));</code> (luo taulun "students" tietokantaan)
    - INSERT - <code>INSERT INTO students(name) VALUES ('Tero');</code> (lisää uusden rivin tauluun ja asettaa "name" -sarakkeen arvoksi 'Tero')
    - SELECT - <code>SELECT * FROM students;</code> (hakee ja näyttää kaikki rivit taulusta "students")
    - UPDATE - <code>UPDATE students SET name='Tero Karvinen' WHERE name='Tero';</code> (päivittää taulun "students" rivin tai rivit, joiden "name" -sarakkeen arvo on 'Tero' ja päivittää rivien sarakkeen arvoksi 'Tero Karvinen')
    - DELETE - <code>DELETE FROM students WHERE name='Liisa';</code> (poistaa rivin tai rivit taulusta "students", joiden "name" -sarakkeen arvo on 'Liisa')
 
  
- OWASP 2017: [A1:2017-Injection](https://owasp.org/www-project-top-ten/2017/A1_2017-Injection)
  
  - hyökkäysvektori:
    - lähes mikä tahansa datalähde voi olla injektiotekijä, kuten ympäristömuuttujat, parametrit, ulkoiset ja sisäiset verkkopalvelut sekä käyttäjät. Injektiovirheet tapahtuvat, kun hyökkääjä voi lähettää haitallista dataa tulkille
  - heikkous:
    - Injektiovirheet yleisiä erityisesti vanhoissa koodeissa. Haavoittuvaisuuksia löytyy usein SQL-, LDAP-, XPath- tai NoSQL-kyselyistä, käyttöjärjestelmän komentoriveiltä, XML-parserilta, SMTP-otsikoilta ja ORM-kyselyistä
    - virheet ovat helppo havaita koodia tutkittaessa
  - vaikutukset:
    - injektio voi johtaa tietojen menetykseen, vääristymiseen tai paljastumiseen luvattomille osapuolille, vastuun menetykseen tai käyttöoikeuden kieltämiseen
    - voi joskus myös johtaa täydelliseen isäntäkoneen haltuunottoon
  - sovellus on altis hyökkäykselle, kun:
    - käyttäjän toimittamaa dataa ei validoitu, suodateta tai puhdisteta sovelluksessa
    - dynaamisia kyselyitä tai parametroimattomia puheluita ilman context-aware escaping, käytetään suoraan tulkissa
    - haitallista dataa käytetään ORM (object-relational mapping) hakuparametreissa saadakseen lisää herkkiä tietueita
    - haitallista dataa käytetään suoraan tai yhdistetään niin, että SQL tai komento sisältää sekä rakenteen että haitallisen datan dynaamisissa kyselyissä, komennoissa tai tallennetuissa proseduureissa
  - lähdekoodin tarkastelu on paras tapa havaita, ovatko sovellukset alttiita injektioille
  - miten estää:
    - on tarpeen pitää data erillään komennoista ja kyselyistä
    - käyttä turvallista API:a, joka välttää tulkin käytön kokonaan tai tarjoaa parametroitavan rajapinnan
    - käytä positiivista tai "whitelist" -puolella olevaa palvelinpohjaista syötteiden validointia
    - käytä LIMIT- ja muita SQL-ohjaimia kyselyissä estääksesi tietueiden massapaljastuksen SQL-injektion tapauksessa
  - Esimerkki: <code>http://esimerkki.com/sovellus/tiliNakyma?id=' or '1'='1</code> - Tämä muuttaa molempien kyselyjen merkitystä palauttamaan kaikki tietueet tilien taulusta
  
- PortSwigger Academy: [SQL injection](https://portswigger.net/web-security/sql-injection)
  - Kaikki muut luvut paitsi ei "Blind SQL injection"
    - SQL-injektio on haavoittuvuus, joka mahdollistaa hyökkääjän puuttumisen sovelluksen tietokantakyselyihin. Tämä voi sallia hyökkääjän nähdä tietoja, joihin heillä ei normaalisti olisi pääsyä esim. käyttäjätunnukset, salasanat, luottotiedot tai muut arkaluontoiset tiedot.
    - SQL-injektiohaavoittuvaisuuksia voi havaita manuaalisesti suorittamalla systemaattisesti testejä jokaiseen sovelluksen syötekohtaan
    - paikkoja, joissa SQL-injektio voi esiintyä:
      - SELECT-kyselyn WHERE-lauseessa
      - UPDATE-lauseissa, päivitettävien arvojen tai WHERE-lauseen sisällä
      - INSERT-lauseissa, lisättävien arvojen sisällä
      - SELECT-lauseissa, taulun tai sarakkeen nimen sisällä
      - SELECT-lauseissa, ORDER BY -lauseessa
    - SQL-injektita:
      - Piilotetun datan noutaminen
      - Sovelluslogiikan kiertäminen
      - UNION-hyökkäykset
      - Blind SQL-injektio
    - Kuinka estää injektiot:
      - käyttämällä parametroituja kyselyitä (prepared statements) sen sijaan, että liittäisit merkkijonoja suoraan kyselyyn
      - jotta paramoitu kysely olisi tehokas SQL-injektion estämisessä, kyselyssä käytettävän merkkijonon on aina oltava kovakoodattu vakio. Sen ei koskaan pidä sisältää muuttuvia tietoja mistään lähteestä.

  - Tämä kappale kannattaa pitää näkyvissä injektioita tehdessä [SQL injection cheat sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)
    - sisältää esimerkkejä hyödyllisestä syntaksista, jotka usein tulevat esiin SQL-injektiohyökkäyksiä suorittaessa
    - Esimerkiksi:
      - Stringin konkatenointi
      - Kommentit
      - Tietokannan versio
      - Tietokannan sisältö
      - DNS-haku
      - ja muita
        
- Vapaaehtoinen: Karvinen 2019: [MitmProxy on Kali and Xubuntu – attack and testing](https://terokarvinen.com/2019/05/22/mitmproxy-on-kali-and-xubuntu-attack-and-testing/?fromSearch=mitmproxy) (Nykyisin asennus 'sudo apt-get install mitmproxy')

## a) CRUD. Tee uusi PostgreSQL-tietokanta ja demonstroi sillä create, read, update, delete (CRUD). Keksi taulujen ja kenttien nimet itse. Taulujen nimet monikossa, kenttien nimet yksikössä, molemmat englanniksi.

Seurasin [Tero Karvisen ohjeita](https://terokarvinen.com/2016/03/05/postgresql-install-and-one-table-database-sql-crud-tutorial-for-ubuntu/).

PostgreSQL oli asennettu aikaisemmin, joten käynnistin tietokannan:

`````
$ sudo systemctl start postgresql                                                                       
$ systemctl status postgresql 

● postgresql.service - PostgreSQL RDBMS
     Loaded: loaded (/lib/systemd/system/postgresql.service; disabled; pr>
     Active: active (exited) since Thu 2023-11-23 16:11:42 EET; 1h 4min a>
   Main PID: 5751 (code=exited, status=0/SUCCESS)
        CPU: 3ms

`````
Komennolla <code>sudo -u postgres createdb $(whoami)</code> sain seuraavan virheilmoituksen:

````
$ sudo -u postgres createdb $(whoami)
could not change directory to "/home/kali": Permission denied
WARNING:  database "postgres" has a collation version mismatch
DETAIL:  The database was created using collation version 2.36, but the operating system provides version 2.37.
HINT:  Rebuild all objects in this database that use the default collation and run ALTER DATABASE postgres REFRESH COLLATION VERSION, or build PostgreSQL with the right library version.
createdb: error: database creation failed: ERROR:  template database "template1" has a collation version mismatch
DETAIL:  The template database was created using collation version 2.36, but the operating system provides version 2.37.
HINT:  Rebuild all objects in the template database that use the default collation and run ALTER DATABASE template1 REFRESH COLLATION VERSION, or build PostgreSQL with the right library version.
````
Collation version mismatch -virheen sain korjattua komennolla <code>sudo -u postgres psql -c "ALTER DATABASE template1 REFRESH COLLATION VERSION;"</code>, tämä päivittää "template1" -tietokannan kollaatiosversion. Uudelleen ajamalla komenon sain seuraavan virheilmoituksen:

`````
$ sudo -u postgres createdb $(whoami)
could not change directory to "/home/kali": Permission denied
createdb: error: database creation failed: ERROR:  database "kali" already exists
            
`````

Tarkistin onko kyseistä tietokantaa jo luotu komennolla <code>sudo -u postgres psql -l</code> ja sellainen oli jo luotu.

`````
  Name    |  Owner   | Encoding | Locale Provider |   Collate   |    Ctype    | ICU Locale | ICU Rules |   Access privileges   
-----------+----------+----------+-----------------+-------------+-------------+------------+-----------+-----------------------
 kali      | postgres | UTF8     | libc            | en_US.UTF-8 | en_US.UTF-8 |            |           | 
`````

Ajoin komennon <code>sudo -u postgres createuser $(whoami)</code>.

`````
$ sudo -u postgres createuser $(whoami)
could not change directory to "/home/kali": Permission denied
createuser: error: creation of new role failed: ERROR:  role "kali" already exists
`````

Tarkistin onko käyttäjä jo luotu.

``````
$ sudo -u postgres psql -c "\du"

                             List of roles
 Role name |                         Attributes                         
-----------+------------------------------------------------------------
 kali      | 

``````

KÄyttäjäkin oli luotu, joten aloitetaan vuorovaikutus tieotkannan kanssa komennolla <code>spql</code>.

#### CREATE TABLE
Aloitan luomalla taulun "cars" komennolla <code>CREATE TABLE cars (id SERIAL PRIMARY KEY, name VARCHAR(200));</code>.

``````
kali=> CREATE TABLE cars (id SERIAL PRIMARY KEY, name VARCHAR(200));
ERROR:  permission denied for schema public
LINE 1: CREATE TABLE cars (id SERIAL PRIMARY KEY, name VARCHAR(200))...
``````
En saanut käyttäjälle "kali" oikeuksia, joten avasin tietokannan käyttäjänä "postgres".

    sudo -u postgres psql 

Aloitin taulun luomisen uudestaan. Ja nyt ei tullut virheitä.

`````
postgres=# CREATE TABLE cars (id SERIAL PRIMARY KEY, name VARCHAR(200));
CREATE TABLE
postgres=#
`````
Tarkistetaan taulu komennolla <code>\d</code>.

``````
postgres=# \d
             List of relations
 Schema |    Name     |   Type   |  Owner   
--------+-------------+----------+----------
 public | cars        | table    | postgres
 public | cars_id_seq | sequence | postgres
(2 rows)

``````

Taulun luominen onnistui. 

#### Create Records: INSERT

Lisäsin tauluun arvon "Toyota".

    INSERT INTO cars(name) VALUES ('Toyota');

Tämän jälkeen lisäsin samalla tavalla arvon "Honda".

#### Read: SELECT

Katsotaan mitä taulusta löytyy:

``````
postgres=# SELECT * FROM cars;
 id |  name  
----+--------
  1 | Toyota
  2 | Honda
(2 rows)

``````
Sieltä löytyy lisäämämme arvot "Toyota" ja "Honda".

#### Update


    UPDATE cars SET name='Kia' WHERE name='Toyota';

Tarkistus:

``````
postgres=# UPDATE cars SET name='Kia' WHERE name='Toyota';
UPDATE 1
postgres=# SELECT * FROM cars;
 id | name  
----+-------
  2 | Honda
  1 | Kia
(2 rows)
``````

Näemme, että Toyota on muuttunut Kiaksi.

#### DELETE

Poistetaan arvo "Kia" tietokannasta.

    DELETE FROM cars WHERE name='Kia';

Tarkistus:

``````
postgres=# SELECT * FROM cars;
 id | name  
----+-------
  2 | Honda
(1 row)

``````

Näemme, että arvo "Kia" on poistettu tietokannasta.

## b) SQLi me. Kuvaile yksinkertainen SQL-injektio, ja demonstroi se omaan tietokantaasi psql-komennolla. Selitä, mikä osa on käyttäjän syötettä ja mikä valmiina ohjelmassa. (Tässä harjoituksessa voit vain kertoa koodista, ei siis tarvitse välttämättä koodata sitä ohjelmaa, joka yhdistää käyttäjän syötteen SQL:n)

Tässä on tietokantamme:

``````
postgres=# SELECT * FROM cars;
 id |  name  
----+--------
  2 | Honda
  4 | Toyota
  5 | Audi
  6 | Ford
  7 | BMW
(5 rows)
``````

Voimme normaalisti hakea tietoa esimerkiksi komennolla <code>SELECT * FROM käyttäjät WHERE nimi = '[käyttäjän syöte]';</code>. Jos saamme laitettau haitallista koodia "käyttäjän syöte" -kohtaa, haavoittuvainen tietokanta voi paljastaa arkaluonteista tietoa, jota käyttäjän ei kuuluisi nähdä. 

Esimerkki injektiosta:

    '' OR '1'='1';

- <code>''</code>:tyhjä merkkijono
- <code>OR</code>: TAI-operaattori, joka palauttaa totuusarvon, jos ainakin yksi ehto on tosi
- <code>'1'='1'</code>: verrataa kahta arvoa keskenään. Palauttaa totuusarvon, jos arvot ovat samat.
- <code>;</code>: sulkee alkuperäisen tietokantakyselyn

Seuraavaksi kokeilemme injektiosyötettä "käyttäjän syöte"- kohtaan.

``````
postgres=# SELECT * FROM cars WHERE name = '' OR '1'='1';
 id |  name  
----+--------
  2 | Honda
  4 | Toyota
  5 | Audi
  6 | Ford
  7 | BMW
(5 rows)
``````

Tietokanta palauttaa meille taulun tiedot kokonaisuudessaa, ilman, että meidän täytyy tietää mitään taulun sisällöstä. Taulun sisältö näytetään koska 1=1 on aina tosi.

## PortSwigger Labs
  
### d) [SQL injection vulnerability allowing login bypass](https://portswigger.net/web-security/sql-injection/lab-login-bypass)

<img src="/images/kuvaus5.png" alt="" title="" width="70%" height="70%">
<img src="/images/login4.png" alt="" title="" width="70%" height="70%">

Kokeilien ensin <code>'' OR '1'='1';:'' OR '1'='1';</code> ja <code>admin:'' OR '1'='1';</code>, mutta ei toiminut. Katsoin labran vinkkejä ja siellä neuvottiin muokkaamaan "username"- parametria vastaamaan "administrator'--". Kun annamme tämän käyttäjänimeksi, ei salasanalla ole väliä. 

Tässä voidaan siis oletta, että kysely näyttää esimekriksi tältä: 

    SELECT * FROM users WHERE username = 'username' AND password = 'password';

Kun lisäämme käyttäjänimeksi <code></code> , näyttäisi haku silloin tältä:

    SELECT * FROM users WHERE username = 'administrator'--' AND salasana = 'salasana';

Koska <code>--</code> on kommenttimerkki SQL-kyselyissä, ei silloin kyselyssä haeta mitään  <code>--</code> jälkeen. Tällöin salasana ohitetaan ja kirjaudutaan administrator- käyttäjänä.

<img src="/images/solved15.png" alt="" title="" width="70%" height="70%">

### e) [SQL injection attack, querying the database type and version on Oracle](https://portswigger.net/web-security/sql-injection/examining-the-database/lab-querying-database-version-oracle)
 

Tehtävässä käytetään hyväksi SQL-injektiohaavoittuvuutta hyväski tuotekategorian suodattimessa. Tehtävässä tulee käyttää UNION-hyökkäystä. UNION-hyökkäys on injektio, jossa yhdistetään kaksi tai useampia kyselyitä yhdeksi taulukoksi, joka tulostetaan.

<img src="/images/kuvaus6.png" alt="" title="" width="70%" height="70%">

Aloitin tutkimalla kategoriaa "Lifestyle". Tehtävä oli haastava, enkä tiennyt mistä aloittaa, joten käytin apuna tehtävän vinkkejä ja [läpikävelyvideota](https://www.youtube.com/watch?v=4sg7ur5Yptk) apuna.

ZAP:issa näimme kyselyn kategoriaa "Lifestyle" painaessa.

<img src="/images/life1.png" alt="" title="" width="70%" height="70%">

Kaappasin pyynnön Requesteriin ja lähdin muokkaamaan URL-kenttää pyynnössä. PortSwiggerin vinkit auttoivat kyselyn muodostamisessa. Katsotaan mitä tapahtuu kyselyllä, johon lisätään <code>'+UNION+SELECT+null,null+FROM+dual--</code>

- <code>UNION</code>: SQL-komento, joka yhdistää kyselyitä
- <code>SELECT+null,null+FROM+dual</code>: valitaan taulusta "dual" arvoja "null, null"
- <code>--</code>: kommentti-merkki

<img src="/images/life2.png" alt="" title="" width="70%" height="70%">

Sivusto palauttaa "200 OK", eli tiedämme, että tietokannassa on kaksi saraketta, josta meidän pitää selvittää mikä käsitteleee kirjaimia. Muutin ensimmäisen sarakkeen "null" kohtaan "abc".

<img src="/images/life3.png" alt="" title="" width="70%" height="70%">

Sivusto palauttaa edelleen "200 OK", eli ensimmäinen sarake käsittelee kirjaimia. Testasin vielä toisen sarakkeen numeroilla.

<img src="/images/life4.png" alt="" title="" width="70%" height="70%">

Sivusto palautti taas "200 OK". Seuraavaksi kokeilin payloadia <code>'+UNION+SELECT+BANNER,+NULL+FROM+v$version--</code>. Tässä <code>v$version</code>- taulustpyritään saamaan <code>BANNER</code> -sarakkeen arvot. Tämä hyökkäys pyrkii hankkimaan tietoja tietokannasta, erityisesti Oracle-tietokannan version. <code>v$version</code> -taulu sisältää tietoa tietokannan versiosta ja asennetusta ohjelmistosta.

<img src="/images/life5.png" alt="" title="" width="70%" height="70%">

ZAPISSA sivusto näyttää antavan vielä "200 OK", joten kopioin injektion ja lisäsin sen verkkosivulla URL:iin, jotta näen paremmin mitä tapahtuu. 

<img src="/images/life6.png" alt="" title="" width="70%" height="70%">

Sivulle on ilmestynyt tietoa tietokannan versiotiedot.

<img src="/images/solved16.png" alt="" title="" width="70%" height="70%">


### f) [SQL injection attack, querying the database type and version on MySQL and Microsoft](https://portswigger.net/web-security/sql-injection/examining-the-database/lab-querying-database-version-mysql-microsoft)

Harjoitus sisältää SQL-injektiohaavoittuvuuden tuotekategorian suodattimessa. Tehtävässä tulee käyttää UNION-hyökkäystä. Tehtävä ratkeaa kun saadaan tietokannan versiotieto.

<img src="/images/kuvaus7.png" alt="" title="" width="70%" height="70%">

Tehtävä vaikuttaa samanlaiselta kuin edellinen.

<img src="/images/alku7.png" alt="" title="" width="70%" height="70%">

Kokeillaan heti samaa hyökkkäystä <code>'+UNION+SELECT+BANNER,+NULL+FROM+v$version--</code>. Harmiksemme selain vastaa: "500 Internal Server Error."

<img src="/images/life7.png" alt="" title="" width="70%" height="70%">

Seuraavaksi kokeilin <code>'+UNION+SELECT+null,+NULL+FROM+dual--</code> sekä lisäsin lisää "null" sarakkeita, mutta selain näytti vielä "500 Internal Server Error". Katsoin vihjeen labrasta ja siinä kehotettiin tarkastelin hieman [payloadeja](https://portswigger.net/web-security/sql-injection/cheat-sheet). Koska kyseessä Microsoftin tietokanta, ppätin kokeilla injektiota <code>'+UNION+SELECT+@@version--</code>, joka on sama injektio kuin aikaisemmin, mutta Microsoftin tietokantaan.

<img src="/images/life8.png" alt="" title="" width="70%" height="70%">

Ei toiminut. Meni hetki, ennen kuin tajusin, että kommenttimerkki täytyy vaihtaa. Täytyy siis käyttää "#". Ajoin injektion <code>'+UNION+SELECT+@@version,+null#</code>

<img src="/images/life8.png" alt="" title="" width="70%" height="70%">

Nyt sivusto sanoo "200 OK". Tarkistetaan, mitlä se näyttää selaimessa.

<img src="/images/solved17.png" alt="" title="" width="70%" height="70%">

Tehtävä suoritettu ja sivun alalaidassa näkyy veriotiedot.

<img src="/images/life10.png" alt="" title="" width="70%" height="70%">


### g) [SQL injection attack, listing the database contents on non-Oracle databases](https://portswigger.net/web-security/sql-injection/examining-the-database/lab-listing-database-contents-oracle)

Harjoitus sisältää SQL-injektiohaavoittuvuuden tuotekategorian suodattimessa. Tehtävässä tulee käyttää UNION-hyökkäystä. Sovelluksessa on kirjautumistoiminto, ja tietokanta sisältää taulun, joka pitää sisällään käyttäjänimet ja salasanat. Tehtävässä täytyy määrittää tämän taulun nimi ja sen sarakkeet, jonka jälkeen haetaan taulun sisältö;  käyttäjien käyttäjänimet ja salasanat. Tehtävä ratkaistaan kun kirjaudutaan sisään administrator- käyttäjänä.

<img src="/images/kuvaus8.png" alt="" title="" width="70%" height="70%">

Ensiksi suuntasin "My acoount sivustolle.

<img src="/images/alku8.png" alt="" title="" width="70%" height="70%">

Siellä on sisäänkirjautuminen.

<img src="/images/login8.png" alt="" title="" width="70%" height="70%">

Suuntasin takaisin ja menin kategoriaan "Pets". Aloin suorittamaan samalla tavalla kuin aikaisemmin. <code>'+UNION+SELECT+null,+NULL+FROM+dual--</code> toimii. 

<img src="/images/pets1.png" alt="" title="" width="70%" height="70%">

Myös <code>'+UNION+SELECT+null,+NULL+FROM+all_tables--</code> toimii. Käytän tässä tehtävässä apuna aikaisempaa [cheatsheetia](https://portswigger.net/web-security/sql-injection/cheat-sheet). 

<img src="/images/pets2.png" alt="" title="" width="70%" height="70%">

<code>'+UNION+SELECT+user,+NULL+FROM+all_tables--</code> toimii, katsoin miltä se näyttää selaimessa ja sieltä paljastui käyttäjä "Peter", mutta ei muita käyttäjiä. 

<img src="/images/pets3.png" alt="" title="" width="70%" height="70%">
<img src="/images/peter.png" alt="" title="" width="70%" height="70%">

En saanut lisääkäyttäjiä näkyviin. Kuitenkin hieman injektiolauseketta pyöritettyäni sain taulut näkyviin. Tauluja oli tuhansia.

    <code>'+UNION+SELECT+table_name,+null+FROM+all_tables--</code>.

<img src="/images/pets5.png" alt="" title="" width="70%" height="70%">
<img src="/images/pets4.png" alt="" title="" width="70%" height="70%">

Käytin selaimessa CMD+F, jolloin voidaan käyttää hakutoimintoa. Hain hakusanalalla "USER". Lopulta löysin mielenkiintoisen taulun.

<img src="/images/pets6.png" alt="" title="" width="70%" height="70%">

Käytämme löytämäämme taulua seuraavaksi hyödyksi.

    <code>'+UNION+SELECT+column_name,NULL+FROM+all_tab_columns+WHERE+table_name='USERS_YIFANH'--</code>

<img src="/images/pets7.png" alt="" title="" width="70%" height="70%">

Löysimme käyttäjänimi ja salasana- sarakkeet. Käytämme seuraavaa payloadia saadaksemme käyttäjänimet ja salasanat:

    'UNION+SELECT+USERNAME_ULJADC,+PASSWORD_EOGGKV+FROM+USERS_YIFANH--

<img src="/images/adminpass.png" alt="" title="" width="70%" height="70%">

Sieltä löyty kolme käyttäjää: administrator, carlos ja wiener. Käytämme administrator käyttäjän tunnuksia kirjautuessa sisälle.

<img src="/images/solved18.png" alt="" title="" width="70%" height="70%">

Tehtävä suoritettu!

### h) [SQL injection UNION attack, determining the number of columns returned by the query](https://portswigger.net/web-security/sql-injection/union-attacks/lab-determine-number-of-columns)

Harjoitus sisältää SQL-injektiohaavoittuvuuden tuotekategorian suodattimessa. Tehtävässä tulee käyttää UNION-hyökkäystä.

<img src="/images/kuvaus9.png" alt="" title="" width="70%" height="70%">
<img src="/images/alku9.png" alt="" title="" width="70%" height="70%">

Kokeilin taas Pets- kategoriasta lähteä liikkeelle payloadilla <code>'+UNION+SELECT+null,+NULL+FROM+dual--</code>, niinkuin aikaisemmissakin. Selain kuitenkin antaa"500 INternal Server Error". Lähdin poistamaan tavaraa ja kokeilin <code>'+UNION+SELECT+null,NULL--</code>, mutta tuloksetta. Lisäsin vielä yhden null-arvon ja sillä saatiin vastaus "200 OK".

<img src="/images/pets8.png" alt="" title="" width="70%" height="70%">

Tarkistin vielä selaimesta ja tehtävä oli suoritettu.

<img src="/images/solved19.png" alt="" title="" width="70%" height="70%">

### i) [SQL injection UNION attack, retrieving data from other tables](https://portswigger.net/web-security/sql-injection/union-attacks/lab-retrieve-data-from-other-tables)

Harjoitus sisältää SQL-injektiohaavoittuvuuden tuotekategorian suodattimessa. Tehtävässä tulee käyttää UNION-hyökkäystä. Tietokannassa on toinen taulu nimeltään "users" (käyttäjät), jossa on sarakkeet nimeltään "username" (käyttäjänimi) ja "password" (salasana). Tehtävän ratkaisemiseksi täytyy suorittaa UNION-hyökkäys, joka noutaa kaikki käyttäjänimet ja salasanat. NÄitä tietoja käyttämällä kirjaudutaan sisään administratorin käyttäjänä.

<img src="/images/kuvaus9.png" alt="" title="" width="70%" height="70%">
<img src="/images/alku9.png" alt="" title="" width="70%" height="70%">

Lähdin kokeilemaan samalla tekniikalla kuin aikaisemmissa:

    '+UNION+SELECT+null,+NULL+FROM+dual--

Kuitenkin tuloksetta. Aloin pudottamaan loppupäästä tekstiä:

    '+UNION+SELECT+null,+NULL--

<img src="/images/tech1.png" alt="" title="" width="70%" height="70%">

Saimme "200 OK". Tehtävässä kerrottiin, että on taulu "users", jossa on sarakkeet nimeltään "username" ja "password", joten lisätään ne payloadiin ja saadaan:

    '+UNION+SELECT+username,password+FROM+users--

<img src="/images/tech2.png" alt="" title="" width="70%" height="70%">

Admin -tunnukset löytyivät. Käytimme tunnuksia sisäänkirjautumiseen.

<img src="/images/tech3.png" alt="" title="" width="70%" height="70%">

Tehtävä suoritettu!

<img src="/images/solved20.png" alt="" title="" width="70%" height="70%">

### j) [SQL injection UNION attack, retrieving multiple values in a single column](https://portswigger.net/web-security/sql-injection/union-attacks/lab-retrieve-multiple-values-in-single-column)

Harjoitus sisältää SQL-injektiohaavoittuvuuden tuotekategorian suodattimessa. Tehtävässä tulee käyttää UNION-hyökkäystä. Tietokannassa on toinen taulu nimeltään "users" (käyttäjät), jossa on sarakkeet nimeltään "username" (käyttäjänimi) ja "password" (salasana). Tehtävän ratkaisemiseksi täytyy suorittaa UNION-hyökkäys, joka noutaa kaikki käyttäjänimet ja salasanat. NÄitä tietoja käyttämällä kirjaudutaan sisään administratorin käyttäjänä.

<img src="/images/kuvaus11.png" alt="" title="" width="70%" height="70%">
<img src="/images/alku11.png" alt="" title="" width="70%" height="70%">

Koska tehtävä on samantyyppinen kuin äskeinen, lähden etenemään taas samalla tekniikalla:

    '+UNION+SELECT+null,+NULL+FROM+dual--

Saimme "500 Internal Server Error". Kokeillaan taas lyhentää payloadia:

    '+UNION+SELECT+null,+NULL--

Nyt saamme "200 OK".

<img src="/images/gifts1.png" alt="" title="" width="70%" height="70%">

Kokeillaan samaa payloadia kuin äskeisessä tehtävässä, sillä tehtävänanto on sama.

    '+UNION+SELECT+username,password+FROM+users--

Saadaan kuitenkin "500 Internal Server Error". En päässyt ilman apua eteenpäin, joten kurkkasin tehtävän vastauksia/vinkkejä. 

    '+UNION+SELECT+NULL,username||'~'||password+FROM+users--

<code>username||'~'||password</code>: Yhdistää "username" ja "password" sarakkeiden tiedot yhdeksi merkkijonoksi, ja tilde-merkil(~) erottaa ne.
    
<img src="/images/gifts2.png" alt="" title="" width="70%" height="70%">

Nyt saimme "200 OK", joten kurkataan selaimeen.

<img src="/images/gifts3.png" alt="" title="" width="70%" height="70%">

Näemme käyttäjät ja salasanat jotka on eroteltu tilde-merkillä. Kopioin admin -tunnukset ja käytän niitä sisäänkirjautumiseen.

<img src="/images/Solved21.png" alt="" title="" width="70%" height="70%">

Tehtävä suoritettu!

## k) Vapaaaehtoinen: Mitmproxy. Demonstroi mitmproxy:n käyttöä terminaalista (TUI tai CLI).
## l) Vapaaehtoinen: Attack lab. Asenna vapaavalintainen kone [Vulnhubista](https://www.vulnhub.com/)https://www.vulnhub.com/ ja murtaudu siihen.


Lähteet:

https://terokarvinen.com/2023/eettinen-hakkerointi-2023/#h5-injected-sequel

https://terokarvinen.com/2016/03/05/postgresql-install-and-one-table-database-sql-crud-tutorial-for-ubuntu/



