<img width="931" height="544" alt="image" src="https://github.com/user-attachments/assets/98755483-2a9b-4243-8aac-8afa04da60cd" /># Harjoitus 3: EternalHomework

## x) Lue/katso/kuuntele ja tiivistä. (Tässä x-alakohdassa ei tarvitse tehdä testejä tietokoneella, vain lukeminen tai kuunteleminen ja tiivistelmä riittää. Tiivistämiseen riittää muutama ranskalainen viiva.)

€ Jaswal 2020: Mastering Metasploit - 4ed: Chapter 1: Approaching a Penetration Test Using Metasploit (kohdasta Conducting a penetration test with Metasploit luvun loppuun eli "Summary" loppuun)
* Metasploit-viitekehystä käytetään tunkeutumistestaukseen hyödyntäen sen exploit, auxiliary ja payload moduuleja.
* Case studyssa kohde skannatiin metasploitin nmapilla ja tunnistettiin Windows 7 haavoittuvuus EternalBlue.
* Metasploitissa käytettiin EternalBlue exploitia, jolla saatiin pääsy kohteeseen ja meterpreter shell.
* Autoroute moduulia käyttämällä saatiin yhteys toisessa verkossa sijaitsevaan Domain Controlleriin.
* Token impersonating hyökkäyksellä päästiin sisään Domain Controlleriin, josta dumpattiin tunnukset ja salasanatiivisteet.

Mitä 'nmap -sn' tekee? Älä arvaa, vaan perustele lähteillä. Mistä tiedät, että käyttämäsi lähde on luotettava?
* `Nmap -sn` ajaa host discoveryn, mutta ei porttiskannausta.
* Host discovery koostuu ICMP echo requestista (pingistä), TCP SYN viestistä porttiin 443, TCP ACK viestistä porttiin 80 ja ICMP timestamp requestista.
* Jos skannattava kohde on samassa verkossa, käytetään ARP pyyntöä.
* Lähde: `man nmap`, https://nmap.org/book/man-host-discovery.html, nmapin virallinen dokumentaatio johon viitataan työkalussa

<img width="406" height="39" alt="image" src="https://github.com/user-attachments/assets/cf15ee9a-ba8b-429f-9e00-b4ffeeb25b59" />


## b) Tallenna porttiskannauksen tuloksia Metasploitin tietokantoihin. Skannaa niin, että Metasploitable tulee mukaan. Kannattaa ottaa mukaan ainakin versioskannaus -sV (joka on banner grabbing plus).

Käynnistin Metasploit tietokannan, sekä Metasploitin.
```
sudo msfdb start
sudo metasploit
```
Metasploit consolista porttiskannasin metasploitablen komennolla `db_nmap -T4 -sV 192.168.56.101`
* `-T4`: Agressiivinen ajoitus
* `-sV`: Versio tunnistus

<img width="931" height="544" alt="image" src="https://github.com/user-attachments/assets/080849b3-93c0-4faf-8045-c5cafe49a590" />

## c) Tarkastele Metasploitin tietokantoihin tallennettuja tietoja komennoilla "hosts" ja "services". Kokeile suodattaa näitä listoja tai hakea niistä.

`db_nmap` tallentaa tulokset metasploitin tietokantaan, josta jälkikäteen on mahdollista tarkastella hosteja ja palveluita skannauksen tuloksista.

Skannasin tällä kertaa vain yhden osoitteen, joten hosteja on yksi.

<img width="921" height="139" alt="image" src="https://github.com/user-attachments/assets/b5785095-98b1-4d62-afae-c3906a7093e1" />

Palvelut olivat samat kuin porttiskannauksessa.

<img width="911" height="472" alt="image" src="https://github.com/user-attachments/assets/60239575-ed98-4c6e-a3cd-124da14ad4b5" />


## d) Internet famous. Etsi Metasploitablen mukana tulevista hyökkäyksistä (en: exploits; search) sellainen, joka on ollut julkisuudessa.
## e) Vertaile nmap:n omaa tiedostoon tallennusta (-oA foo) ja db_nmap:n tallennusta tietokantoihin. Mitkä ovat eri tiedostomuotojen ja Metasploitin tietokannan hyvät puolet?
## f) Murtaudu Metasploitablen vsftpd-palveluun
## g) Kerää levittäytymisessä (lateral movement) tarvittavaa tietoa metasploitablesta. Analysoi tiedot. Selitä, miten niitä voisi hyödyntää.
## h) Murtaudu Metasploitableen jollain toisella tavalla. (Jos tämä kohta on vaikea, voit tarvittaessa turvautua verkosta löytyviin läpikävelyohjeisiin. Merkitse silloin raporttiin, missä määrin tarvitsit niitä).
## i) Demonstroi Meterpretrin ominaisuuksia.
## j) Tallenna shell-sessio tekstitiedostoon script-työkalulla (script -fa log001.txt) tai tmux:lla.
## k) Pivot point. Laita kaikki harjoituksen tiedostot (script -fa, nmap -oA...) samaan kansioon. Hae sopiva pivot point (sovellus, versio, osoite, MAC-numero) 'grep -r' -komennolla. Keksi uskottava esimerkkikysymys, johon haet vastausta.
## l) Attaaack! Mitä Mitre Attack taktiikoita ja tekniikoita käytit tässä harjoituksessa? (Tässä alakohdassa "Attaack!" ei tarvitse tehdä lisää testejä koneella, koska testit on jo tehty.)
