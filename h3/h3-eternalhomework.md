# Harjoitus 3: EternalHomework

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
* Lähde: `man nmap`, https://nmap.org/book/man-host-discovery.html, nmapin virallinen dokumentaatio

## b) Tallenna porttiskannauksen tuloksia Metasploitin tietokantoihin. Skannaa niin, että Metasploitable tulee mukaan. Kannattaa ottaa mukaan ainakin versioskannaus -sV (joka on banner grabbing plus).
## c) Tarkastele Metasploitin tietokantoihin tallennettuja tietoja komennoilla "hosts" ja "services". Kokeile suodattaa näitä listoja tai hakea niistä.
## d) Internet famous. Etsi Metasploitablen mukana tulevista hyökkäyksistä (en: exploits; search) sellainen, joka on ollut julkisuudessa.
## e) Vertaile nmap:n omaa tiedostoon tallennusta (-oA foo) ja db_nmap:n tallennusta tietokantoihin. Mitkä ovat eri tiedostomuotojen ja Metasploitin tietokannan hyvät puolet?
## f) Murtaudu Metasploitablen vsftpd-palveluun
## g) Kerää levittäytymisessä (lateral movement) tarvittavaa tietoa metasploitablesta. Analysoi tiedot. Selitä, miten niitä voisi hyödyntää.
## h) Murtaudu Metasploitableen jollain toisella tavalla. (Jos tämä kohta on vaikea, voit tarvittaessa turvautua verkosta löytyviin läpikävelyohjeisiin. Merkitse silloin raporttiin, missä määrin tarvitsit niitä).
## i) Demonstroi Meterpretrin ominaisuuksia.
## j) Tallenna shell-sessio tekstitiedostoon script-työkalulla (script -fa log001.txt) tai tmux:lla.
## k) Pivot point. Laita kaikki harjoituksen tiedostot (script -fa, nmap -oA...) samaan kansioon. Hae sopiva pivot point (sovellus, versio, osoite, MAC-numero) 'grep -r' -komennolla. Keksi uskottava esimerkkikysymys, johon haet vastausta.
## l) Attaaack! Mitä Mitre Attack taktiikoita ja tekniikoita käytit tässä harjoituksessa? (Tässä alakohdassa "Attaack!" ei tarvitse tehdä lisää testejä koneella, koska testit on jo tehty.)
