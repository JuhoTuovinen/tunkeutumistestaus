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
- PortSwigger Academy: SQL injection
  - Kaikki muut luvut paitsi ei "Blind SQL injection"
  - Tämä kappale kannattaa pitää näkyvissä injektioita tehdessä [SQL injection cheat sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)
- Vapaaehtoinen: Karvinen 2019: [MitmProxy on Kali and Xubuntu – attack and testing](https://terokarvinen.com/2019/05/22/mitmproxy-on-kali-and-xubuntu-attack-and-testing/?fromSearch=mitmproxy) (Nykyisin asennus 'sudo apt-get install mitmproxy')

## a) CRUD. Tee uusi PostgreSQL-tietokanta ja demonstroi sillä create, read, update, delete (CRUD). Keksi taulujen ja kenttien nimet itse. Taulujen nimet monikossa, kenttien nimet yksikössä, molemmat englanniksi.
## b) SQLi me. Kuvaile yksinkertainen SQL-injektio, ja demonstroi se omaan tietokantaasi psql-komennolla. Selitä, mikä osa on käyttäjän syötettä ja mikä valmiina ohjelmassa. (Tässä harjoituksessa voit vain kertoa koodista, ei siis tarvitse välttämättä koodata sitä ohjelmaa, joka yhdistää käyttäjän syötteen SQL:n)
## PortSwigger Labs

- c) (Alakohta c poistettu, tämänhän ratkoimme jo aiemmin: [SQL injection vulnerability in WHERE clause allowing retrieval of hidden data)](https://portswigger.net/web-security/sql-injection/lab-retrieve-hidden-data)
- d) [SQL injection vulnerability allowing login bypass](https://portswigger.net/web-security/sql-injection/lab-login-bypass)
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

