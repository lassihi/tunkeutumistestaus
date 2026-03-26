# Harjoitus 1: Kybertappoketju
Kurssi: https://terokarvinen.com/tunkeutumistestaus/ \
Tehtävänanto: https://terokarvinen.com/palvelinten-hallinta/#h1-viisikko

## x) Lue/katso/kuuntele ja tiivistä 
**Herrasmieshakkerit tai Darknet Diaries, yksi vapaavalintainen jakso jommasta kummasta**
* Jakso: Valokuvien vainukoira, vieraana Teemu M. Nieminen | 0x41
  * Jakson alussa käytiin läpi ajankohtaisia aiheita
    * Signalin perustaja Moxy Marlinspiken luoma yksityisyyteen perustuva tekoälymalli Confer
    * EU-INC, joka pyrkii yhtenäistämään yrittäjyyden Euroopassa
  * Vieraana GEOINTiin erikoistunut avointen lähteiden tutkija Teemu M. Nieminen kuvaamassa valokuvien paikantamisprosessia

**Hutchins et al 2011: Intelligence-Driven Computer Network Defense Informed by Analysis of Adversary Campaigns and Intrusion Kill Chains**
* Kill chain, eli tappoketju on alunperin sotilaallinen käsite, joka kuvaa hyökkäyksen vaiheita ja vaiheellisuutta
* Hutchins paperissaan esittää mallin tappoketjusta, joka on laadittu kuvaamaan tietoverkkojen hyökkäysten vaiheita: Kybertappoketju
* Kybertappoketjun vaiheet: Reconnaisance, Weaponization, Delivery, Exploitation, Installation, Command and Control, Actions on Objectives

**Santos et al: The Art of Hacking (Video Collection): 4.3 Surveying Essential Tools for Active Reconnaissance**

**KKO 2003:36**
* 19.11.1998 nuoren atk-harrastajan porttiskannaus osunut Osuuspankkikeskus-OPK osuuskunnan tietojärjestelmään
* Kohteena ollut yritys nostanut syytteen asiasta käräjäoikeudessa, josta se siirtynyt aina valitusten kautta korkeimpaan oikeuteen
* Korkein oikeus katsonut, että syytetty on oikeudettomasti yrittänyt tunkeutua tietojärjestelmään

## a) Asenna Kali virtuaalikoneeseen. (Jos asennuksessa ei ole mitään ongelmia tai olet asentanut jo aiemmin, tarkkaa raporttia tästä alakohdasta ei tarvita).

Asennus onnistui hyvin 

## b) Irrota Kali-virtuaalikone verkosta. Todista testein, että kone ei saa yhteyttä Internetiin (esim. 'ping 8.8.8.8')

## c) Porttiskannaa 1000 tavallisinta tcp-porttia omasta koneestasi (nmap -T4 -A localhost). Selitä komennon paramterit. Analysoi ja selitä tulokset.
Ajoin annetun komennon pääkäyttäjänä, jotta porttiskannaus toimisi optimaalisesti
```
sudo nmap -T4 -A localhost
```
* `sudo nmap`: nmap-ohjelman ajaminen pääkäyttäjänä
* `-T4`: käytetään agressiivista ajoitusta, nopeuttaa skannauksen ajoa
* `-A`: käyttöjärjestelmän tunnistus, versioiden tunnistus, skriptien käyttö ja traceroute
* `localhost`: skannauksen kohdeosoite

## d) Asenna kaksi vapaavalintaista demonia ja skannaa uudelleen. Analysoi ja selitä erot.

## e) Ratkaise vapaavalintainen kone HackTheBoxista. Omalle tasolle sopiva, useimmille varmaan Starting Pointista. Valitse kone, jota et ole ratkaissut vielä. Ei tunnilla näytetty Meow. (Propellihatuille: jos teet vaikeampia ei-starting-point koneita, niin retired tai vastaava kone, josta saa julkaista writeupin).

Tein HackTheBoxin Cap-koneen, sillä se nopealla vilkaisulla näytti olevan suosituin retired kategoriasta. Koneen kuvauksessa kerrotaan HTTP-palvelimessa olevan IDOR-haavoittuvuus, joka mahdollistaa tunnusten löytämisen, joilla päästään linux koneen rootiksi. (https://app.hackthebox.com/machines/Cap)

Latasin ensiksi koneeseen liitetyn VPN konfiguraation, jonka jälkeen yhdistin VPN palvelimeen suoraan NetworkManagerin kautta.

*Kysymys 1: How many TCP ports are open?*

Porttiskannasin kohdekoneen, `sudo nmap `

Nmap näyttää, että avoimia portteja on 3.


## Lähteet
https://nmap.org/book/man-performance.html
