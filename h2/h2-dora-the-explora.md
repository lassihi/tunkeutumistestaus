# Harjoitus 2: DORA the Explora
Kurssi: https://terokarvinen.com/tunkeutumistestaus/ \
Tehtävänanto: https://terokarvinen.com/tunkeutumistestaus/#h2-dora-the-explora

## x) Lue/katso/kuuntele ja tiivistä
* Buuri 2026: DORA and TLPT testing - Lecture for Haaga-Helia on 31 March 2026 (pdf, 2 MB)
* DORA (Regulation ... on digital operational resilience for the financial sector) (vain nämä kaksi artiklaa):
  * Article 26 "Advanced testing of ICT tools, systems and processes based on TLPT"
  * Article 27 "Requirements for testers for the carrying out of TLPT"
* TIBER-FI procedures and guidelines (pdf, 1 MB) (vain tämä kohta):
  *5.4 Testing phase: Red team testing (johdantokappale suoraan 5.4 alta, "5.4.1 Red team test plan creation" alkuun asti)
* Vapaaehtoinen bonus: Buuri 2026: D26 - Releasing Your Inner TIBER in Regulated Adversary Simulations. Video, 45 min. Disobey 2026.

## a) Asenna Metasploitable 2 virtuaalikoneeseen.

Tein tämän kohdan tunnilla, joten tässä vaiheet muistista:
1. Asensin metasploitable2 zip-tiedoston sivustolta https://www.rapid7.com/products/metasploit/metasploitable/
2. Purin ladatun zip-tiedoston, `unzip metasploitable-linux-2.0.0.zip`
3. Loin VirtualBoxissa uuden virtuaalikoneen seuraavilla vaihtoehdoilla:
  * VM Name: Metasploitable
  * OS: Linux
  * OS Distribution: Ubuntu
  * OS Version: Ubuntu (64-bit)
  * Base Memory: 1536 MB
  * Number of CPUs: 2
  * Kaikki muut valinnat oletuksena

4. Kun virtuaalikone oli luotuna vaihdoin sen levykuvaksi ladatussa zip-tiedostossa sijainneen Metasploitable.vmdk-tiedoston
5. Käynnistin virtuaalikoneen ja se käynnistyi shelliin.

<img width="719" height="397" alt="image" src="https://github.com/user-attachments/assets/7b634ea7-de8d-46df-90d5-27297b779e93" />

## b) Tee Kalin ja Metasploitablen välille virtuaaliverkko. Jos säätelet VirtualBoxista Kali saa yhteyden Internettiin, mutta sen voi laittaa pois päältä. Kalin ja Metasploitablen välillä on host-only network, niin että porttiskannatessa ym. koneet on eristetty intenetistä, mutta ne saavat yhteyden toisiinsa.

Käytän kurssilla kakkoskannettavaani tehtävien tekoon, jossa Kali on host-käyttöjärjestelmänä. Siispä valitsin käyttää Metasploitable-virtuaalikoneessa Host-only verkkoadapteria. Tällöin virtuaalikone saa ainoastaan hostiin yhteyden ja kalissa voin sammuttaa muut yhteydet verkkoon.

Tein myös tämän kohdan tunnilla, joten myös tässä asennuksen kohdat muistista:
1. Sammutin Metasploitable virtuaalikoneen
2. Loin uuden Host-only verkon VirtualBoxissa
   * File -> Tools -> Network
  
   <img width="1114" height="147" alt="image" src="https://github.com/user-attachments/assets/994a0750-d9a5-4c9e-bd85-335db6848573" />
3. Lisäsin Metasploitable-virtuaalikoneen Host-only verkkoon
   <img width="967" height="403" alt="image" src="https://github.com/user-attachments/assets/90ada30a-397e-4465-8006-c30be88a5f11" />

## c) Harjoittelemme omassa virtuaaliverkossa, jossa on Kali ja Metaspoitable. Osoita testein, että 1) koneet eivät saa yhteyttä Internetiin 2) Koneet saavat yhteyden toisiinsa.

Sammutin ensiksi verkkoyhteyden Kalin network managerista valitsemalla "disconnect". Tämän testasin saanko pingattua googlen ja cloudflaren palvelimia.

<img width="508" height="122" alt="image" src="https://github.com/user-attachments/assets/262932db-861b-4055-86c2-2d282abd3cb1" />

Tein saman testin Metasploitablella.

<img width="439" height="63" alt="image" src="https://github.com/user-attachments/assets/6b6ef7f5-5448-4a7c-9f18-d7e143e7e57a" />

Kumpikaan ping ei mennyt läpi, joka todistaa koneiden olevan irti internetistä.

Testasin seuraavaksi samalla metodilla ovatko kali ja metasploitable yhteydessä toisiinsa.

`ip a` -komento kertoi kalin IP:n olevan 192.168.56.1 ja metasploitablen IP:n olevan 192.168.56.101 vboxnet0 virtuaaliverkossa.

<img width="836" height="294" alt="image" src="https://github.com/user-attachments/assets/7404d392-6716-4b30-bc54-c2d05f7b32ed" />

<img width="709" height="176" alt="image" src="https://github.com/user-attachments/assets/793ff547-f2c9-4079-9ed5-89a5519b4878" />

Metasploitablen pingaus kalilla:

<img width="566" height="183" alt="image" src="https://github.com/user-attachments/assets/2d847d1b-787a-490a-8143-c63b27da00c8" />

Kalin pingaus metasploitablella:

<img width="584" height="160" alt="image" src="https://github.com/user-attachments/assets/22343a21-37cb-4491-b121-755b2c36bccb" />

Ping onnistui kumpaankin suuntaan, joten kali ja metasploitable ovat yhteydessä toisiinsa.

## d) Etsi Metasploitable porttiskannaamalla (nmap -sn). Tarkista selaimella, että löysit oikean IP:n - Metasploitablen weppipalvelimen etusivulla lukee Metasploitable.

Skannasin nmapilla vboxnet0 virtuaaliverkon, `sudo nmap -sn 192.168.56.1/24`. `-sn`-optio tekee ping-skannauksen ilman porttiskannausta.

<img width="939" height="257" alt="image" src="https://github.com/user-attachments/assets/bce78ea2-ee2b-4590-976c-fb250e51544a" />

Skannaus löysi verkosta kolme hostia, joista kalin ja metasploitablen IP:t oli viime kohdasta tuttuja. Kolmas IP-osoite 192.168.56.100 kuuluu Host-only verkkoon automaattisesti luodulle DHCP-palvelimelle.

<img width="931" height="597" alt="image" src="https://github.com/user-attachments/assets/61248971-9d2b-470a-95d8-4af01f99b5b4" />

Siirryin vielä metasploitablen weppipalvelimelle tarkaakseni, että IP-osoite on oikea.

<img width="980" height="475" alt="image" src="https://github.com/user-attachments/assets/e8bfaf26-1c96-4edf-9df5-553bff722cf7" />

Oikea sivusto tuli esille.
## e) Porttiskannaa Metasploitable huolellisesti ja kaikki portit (nmap -A -T4 -p-). Poimi 2-3 hyökkääjälle kiinnostavinta porttia. Analysoi ja selitä tulokset näiden porttien osalta. Voit hakea analyysin tueksi tietoa verkosta, muista merkitä lähteet.

## f) Vapaaehtoinen bonus: Sisään vaan. Pääsetkö murtautumaan Metasploitableen?

## g) Vapaaehtoinen bonus: jos haluat, voit jo kokeilla metasploit-hyökkäysohjelmaa omaan harjoitusmaaliisi. Tätä katsotaan myöhemmin yhdessäkin. (Muista irrottaa kone Internetistä kokeilujen ajaksi. 'sudo msfdb init', 'sudo msfconsole').
