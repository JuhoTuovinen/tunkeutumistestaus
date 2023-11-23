# Injected Sequel
Juho Tuovinen TARKISTA POSTGRES

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
  - Tämä kappale kannattaa pitää näkyvissä injektioita tehdessä [SQL injection cheat sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)
- Vapaaehtoinen: Karvinen 2019: [MitmProxy on Kali and Xubuntu – attack and testing](https://terokarvinen.com/2019/05/22/mitmproxy-on-kali-and-xubuntu-attack-and-testing/?fromSearch=mitmproxy) (Nykyisin asennus 'sudo apt-get install mitmproxy')

## a) CRUD. Tee uusi PostgreSQL-tietokanta ja demonstroi sillä create, read, update, delete (CRUD). Keksi taulujen ja kenttien nimet itse. Taulujen nimet monikossa, kenttien nimet yksikössä, molemmat englanniksi.

https://terokarvinen.com/2016/03/05/postgresql-install-and-one-table-database-sql-crud-tutorial-for-ubuntu/
PostgreSQL oli asennettu aikaisemmin. Käynnistin tietokannan:

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
Collation version mismatch -virheen sain korjattua komennolla <code>sudo -u postgres psql -c "ALTER DATABASE template1 REFRESH COLLATION VERSION;"</code>, jolloin uudelleen ajamalla komenot sain seuraavan virheilmoituksen:

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

Tarkiston onko käyttäjä jo luotu.

``````
$ sudo -u postgres psql -c "\du"

                             List of roles
 Role name |                         Attributes                         
-----------+------------------------------------------------------------
 kali      | 

``````

Aloitetaan vuorovaikutus tieotkannan kanssa komennolla <code>spql</code>.

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
  
- d) [SQL injection vulnerability allowing login bypass](https://portswigger.net/web-security/sql-injection/lab-login-bypass)

<img src="/images/kuvaus5.png" alt="" title="" width="70%" height="70%">

Kokeilien ensin <code>'' OR '1'='1';:'' OR '1'='1';</code> ja <code>admin:'' OR '1'='1';</code>, mutta ei toiminut. Katsoin labran vinkkejä ja siellä neuvottiin muokkaamaan "username"- parametria vastaamaan "administrator'--". Kun annamme tämän käyttäjänimeksi, ei salasanalla ole väliä. 

Tässä voidaan siis oletta, että kysely näyttää esimekriksi tältä: 

    SELECT * FROM users WHERE username = 'username' AND password = 'password';

Kun lisäämme käyttäjänimeksi <code></code> , näyttäisi haku silloin tältä:

    SELECT * FROM users WHERE username = 'administrator'--' AND salasana = 'salasana';

Koska <code>--</code> on kommenttimerkki SQL-kyselyissä, ei silloin kyselyssä haeta mitään  <code>--</code> jälkeen. Tällöin salasana ohitetaan ja kirjaudutaan administrator- käyttäjänä.

<img src="/images/solved15.png" alt="" title="" width="70%" height="70%">

- e) [SQL injection attack, querying the database type and version on Oracle](https://portswigger.net/web-security/sql-injection/examining-the-database/lab-querying-database-version-oracle)
- f) [SQL injection attack, querying the database type and version on MySQL and Microsoft](https://portswigger.net/web-security/sql-injection/examining-the-database/lab-querying-database-version-mysql-microsoft)
- g) [SQL injection attack, listing the database contents on non-Oracle databases](https://portswigger.net/web-security/sql-injection/examining-the-database/lab-listing-database-contents-oracle)
- h) [SQL injection UNION attack, determining the number of columns returned by the query](https://portswigger.net/web-security/sql-injection/union-attacks/lab-determine-number-of-columns)
- i) [SQL injection UNION attack, retrieving data from other tables](https://portswigger.net/web-security/sql-injection/union-attacks/lab-retrieve-data-from-other-tables)
- j) [SQL injection UNION attack, retrieving multiple values in a single column](https://portswigger.net/web-security/sql-injection/union-attacks/lab-retrieve-multiple-values-in-single-column)

## k) Vapaaaehtoinen: Mitmproxy. Demonstroi mitmproxy:n käyttöä terminaalista (TUI tai CLI).
## l) Vapaaehtoinen: Attack lab. Asenna vapaavalintainen kone [Vulnhubista](https://www.vulnhub.com/)https://www.vulnhub.com/ ja murtaudu siihen.


Lähteet:

https://terokarvinen.com/2023/eettinen-hakkerointi-2023/#h5-injected-sequel

https://terokarvinen.com/2016/03/05/postgresql-install-and-one-table-database-sql-crud-tutorial-for-ubuntu/



