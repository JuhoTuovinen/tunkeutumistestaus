# Sieppaa ja analysoi verkkoliikennettä.

Tässä tehtävässä tutkin oman verkkoni verkkoliikenettä hyödyntämällä Wireshark työkalua.

Tietokoneen speksit, jolla suoritan toimenpiteen:
- Apple MacBook 2015
- macOs Monterey versio 12.7
- Ram -muistia 16GB
- levytilaa/muistia vapaana 49,11 GB.

Ohjelmistot
- UTM Version 4.1.6 (75)
- VM OS: Kali Linux 2023.3 (kali-rolling)

Kello 11.35
Ensin päivitin paketinhallintajärjestelmän virtuaalikoneessani.

    sudo apt update

Kello 11.39
Asensin Wiresharkin virtuaalikoneelle.

    sudo apt install wireshark

Käynnistin Wiresharkin komentokehotteessa

    wireshark

Otin yhteyttä verkkoselaimessa osoitteeseen google.com ja tallensin saamani tulokset kuvankaappauksella. Kuvasta näkee, että DNS kyselyitä on tehty.

[Kuva poistettu tietoturvasyistä 6.10.2023. Tehtävä päivittyy pian]




## Lähteet

Karvinen, Tero: Oppitunnit 2023-10-23, tunkeutumistestaus, h0-alkutesti (https://terokarvinen.com/2023/eettinen-hakkerointi-2023/#h0-alkutesti)

ChatGPT (https://chat.openai.com/)
