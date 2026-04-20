# Harjoitus 4: Täysin laillinen sertifikaatti

Kurssi: https://terokarvinen.com/tunkeutumistestaus/ \
Tehtävänanto: https://terokarvinen.com/tunkeutumistestaus/#h4-taysin-laillinen-sertifikaatti

## x) Lue/katso ja tiivistä. (Tässä x-alakohdassa ei tarvitse tehdä testejä tietokoneella, vain lukeminen tai kuunteleminen ja tiivistelmä riittää.)

OWASP 2021: OWASP Top 10:2021: [A01:2021 – Broken Access Control](https://owasp.org/Top10/2021/A01_2021-Broken_Access_Control/index.html) (IDOR ja path traversal ovat osa tätä)
* Vuonna 2021 Broken access control yleisin web-haavoittuvuus, testatuista sovelluksista 94 % olivat haavoittuvaisia.
* Broken access control haavoittuvuus tarkoittaa, että käyttäjä pääsee käsiksi resursseihin joihin hänellä ei tulisi olla oikeuksia
* Broken access control -luokka on laaja ja kattaa kymmeniä haavoittuvuustyyppejä alleen.
* Haavoittuvuuksien ehkäisyn kannalta oleellisia asioita ovat mm. pääsynhallinnan palvelinpään tarkistukset, least privilege ja deny by default -perjaatteet, haavoittuvuus-skannaus ja rate limitit.
* Haavoittuvuus on selvästi triviaali paikata, mutta yleinen sillä kun on suuria määriä erilaisia sivuja ja endpointeja, niin joku niistä helposti unohtuu pääsynhallinnan ulkopuolelle.

PortSwigget Academy: [Insecure direct object references](https://portswigger.net/web-security/access-control/idor) (IDOR), [Path traversal](https://portswigger.net/web-security/file-path-traversal) ja [Cross-site scripting](https://portswigger.net/web-security/cross-site-scripting)
* IDOR
  * Pääsynhallinnan haavoittuvuus.
  * Haavoittuvuus syntyy, kun ohjelma käyttää suoraan käyttäjän syöttämää tietoa objektien hakemiseen ja käyttämiseen.
  * Yleisesti käytetään horisontaaliseen oikeuksien laajentamiseen, mutta voi ilmetä myös liittyen vertikaaliseen oikeuksien laajentamiseen.
  * Tyypillisessä skenaariossa muokataan URL-parametrissa olevaa dataa, esimerkiksi vaihdetaan haettava id.
    * Käytin kyseistä haavoittuvuutta ensimmäisessä kotitehtävässä HackTheBox:n Cap-koneeseen (https://github.com/lassihi/tunkeutumistestaus/blob/main/h1/h1-kybertappoketju.md#task-3-are-you-able-to-get-to-other-users-scans)
* Path traversal
  * Pääsynhallinnan haavoittuvuus.
  * Haavoittuvuus mahdollistaa käyttäjän navigoimaan palvelimen tiedostorakennetta, sekä lukemaan ja mahdollisesti manipuloimaan siellä sijaitsevia tiedostoja.
  * Haavoittuvuutta tyypillisesti hyödynnetään syöttämällä halutun resurssin suhteellinen tai absoluuttinen sijainti URL-osoitteen parametriksi.
  * Haavoittuvuus ehkäistään välttämällä kokonaan käyttäjän syötteen syöttämistä tiedostojärjestelmien ohjelmointirajapinnoille.
* Cross-site scripting
  * Haavoittuvuus, jossa hyökkääjä pääsee lisäämään haitallista javascriptiä verkkosivulle, niin että se ajetaan käyttäjän selaimessa.
  * Mahdollistaa hyökkääjälle täyden hallinnan käyttäjän istunnosta.
  * XSS tyypit:
    * Reflected XSS: Haitallinen koodi tulee HTTP-pyynnöstä
    * Stored XSS: Haitallinen koodi tulee sivuston tietokannasta
    * DOM-based XSS: Haavoittuvuus sijaitsee asiakaspään koodissa
  * Haavoittuvuus ehkäistään suodattamalla käyttäjien syötteet, enkoodaamalla tuloste asianmukaisesti, käyttämällä oikeita headereita ja viimekädessä selainten Conten Security Policyn avulla

## Totally Legit Sertificate. Asenna OWASP ZAP, generoi CA-sertifikaatti ja asenna se selaimeesi. Laita ZAP proxyksi selaimeesi, sieppaamaan kuvia ja osoita, että hakupyynnöt ilmestyvät käyttöliittymään.

Päivitin paketit ja asensin ZAPin.

```
sudo apt-get update
sudo apt-get install zaproxy
```

Avasin ZAPin, `zaproxy`. Valitsin oletusasetukset session tallennuksesta.

<img width="1023" height="806" alt="image" src="https://github.com/user-attachments/assets/c6b6b28d-4bef-4af0-a098-2d5044b7524e" />

Loin uuden Root CA sertifikaatin Tools -> Options -> Network -> Server Certificates alta napista "Generate". Tallensin uuden sertifikaatin kotihakemistooni "Save" napista.

Sertifikaatin asentamiseksi Firefoxiin avasin selaimen certificate managerin ja kohdasta authorities importasin sertifikaattitiedoston.

Lisätäkseni ZAPin proxyksi Firefoxiin avasin yhteysasetukset yleisten asetusten alta. Valitsin manuaalisen proxykonfiguraation ja lisäsin ZAPin osoitteen ja portin. Oikea osoite ja portti löytyi ZAPin yleisnäkymän alalaidasta.

<img width="753" height="824" alt="image" src="https://github.com/user-attachments/assets/b762c903-adfa-4b75-90fd-cf9b2433b5b9" />

Asetin ZAPin kaappaaman kuvia valitsemalla ZAPin asetuksista "Process images in HTTP requests/responses".

<img width="929" height="577" alt="image" src="https://github.com/user-attachments/assets/90664ab0-39f5-49f9-bde8-85794c0f1661" />

Todistaakseni että proxy toimii, aloitin uuden session ZAPissa ja uudelleenlatasin kurssisivun https://terokarvinen.com/tunkeutumistestaus/. Pyyntö tuli näkyviin historiaan ja sitä klikkaamalla sain avattua myös palvelimen lähettämän vastauksen salaamattomassa muodossa.

<img width="1915" height="1040" alt="image" src="https://github.com/user-attachments/assets/36c1c667-e414-4d4c-aa35-ef4eb7ec7aec" />

Myös sivun lataamia kuvia näkyi ZAPissa.

<img width="1328" height="867" alt="image" src="https://github.com/user-attachments/assets/671d3d02-2805-4929-a7c4-96d836829e65" />

## b) Kettumaista. Asenna "FoxyProxy Standard" Firefox Addon, ja lisää ZAP proxyksi siihen. Käytä FoxyProxyn "Patterns" -toimintoa, niin että vain valitsemasi weppisivut ohjataan Proxyyn.

Käytin FoxyProxyn asentamiseen ja ZAPin lisäämiseen siihen Proxywayn artikkelia https://proxyway.com/guides/foxyproxy-chrome-firefox.

Lisäsin FoxyProxy Standardin Firefoxiin lisäosakaupasta addons.mozilla.org, ja annoin sille vaadittavat oikeudet toimia selaimessa.

<img width="473" height="323" alt="image" src="https://github.com/user-attachments/assets/cc9510a6-51d8-49fb-9756-6b7a8f456d95" />

Kun FoxyProxy oli selaimessa, avasin sen asetukset kohtaan "Proxies" ja lisäsin ZAPin seuraavilla asetuksilla:

<img width="1206" height="500" alt="image" src="https://github.com/user-attachments/assets/3c44298b-63b2-44c6-97ae-c61b2f87fd21" />

Lisäsin proxyyn patternin proxyasetusten alta. Laitoin sen lähettämään vain kurssisivun, sekä kurssisivun alla olevat resurssit ZAPiin.

<img width="878" height="284" alt="image" src="https://github.com/user-attachments/assets/ca940e40-5754-4c52-a148-b10082065fae" />

Asetin patternit päälle klikkaamalla "Proxy by Patterns".

Loin ZAPissa uuden session, ja selailin kurssivulla, sekä muilla verkkosivuilla kuten githubissa. Tarkastin ZAPin ja se näytti vain pyynnöt jotka kuuluivat filtteriin, joten filtteri toimii.

<img width="941" height="880" alt="image" src="https://github.com/user-attachments/assets/9a95e679-6802-4859-85fb-6b37babc23c8" />

## PortSwigger Labs. Ratkaise tehtävät. Selitä ratkaisusi: mitä palvelimella tapahtuu, mitä eri osat tekevät, miten hyökkäys löytyi, mistä vika johtuu.

Ennen tehtäviä loin tunnukset PortSwiggeriin päästäkseni käsiksi labroihin.

### Cross Site Scripting (XSS)

#### c) Reflected XSS into HTML context with nothing encoded

Lab: https://portswigger.net/web-security/cross-site-scripting/reflected/lab-html-context-nothing-encoded

Labra sisältää blogisivuston, joka näyttää sisältävän muumataman artikkelin, sekä hakukentän. Artikkeleita on myös mahdollista kommentoida. Keskityin heti aluksi hakukenttään ja kokeilin sen toiminnallisuutta. Hain sanaa "history".

<img width="787" height="941" alt="image" src="https://github.com/user-attachments/assets/ac9c7394-29fc-4374-8ed4-b42545f26ed6" />

Hakemani sana liitettiin sivun HTML:ään, joten lähdin kokeilemaan jos siinä olisi XSS-haavoittuvuus.

Lisäsin hakukenttään seuraavan HTML:n `<script>alert("hello world")</script>`.

<img width="1223" height="939" alt="image" src="https://github.com/user-attachments/assets/e5d110de-faef-42a9-9c84-324f8330d8e3" />

XSS onnistui. Hakemani teksti lisättiin sellaisenaan sivulle, jolloin selaimen ladatessa sivun se näkee script-tagien sisällä olevan tekstin JavaScript-koodina ja ajaa sen.

#### d) Stored XSS into HTML context with nothing encoded

Lab: https://portswigger.net/web-security/cross-site-scripting/stored/lab-html-context-nothing-encoded

Samantyylinen blogisivusto avautui, kuin edellisessä labrassa, mutta ilman hakukenttää. Lähdin heti testaamaan kommentointia, sillä haavoittuvaisena se voi johtaa stored XSS:ään. Kommentit lisätään sivuston tietokantaan, josta ne tarjoillaan kävijoille.

Lähetin yhteen artikkeleista kommentin, jonka tekstikenttään lisäsin XSS payloadin.

<img width="741" height="622" alt="image" src="https://github.com/user-attachments/assets/8143e31b-2dc1-402e-a868-cd6ba1d363c8" />

Lähetin kommentin ja sain labran läpi.

<img width="1225" height="438" alt="image" src="https://github.com/user-attachments/assets/5807557a-8b9f-43d1-9e09-4226f1dbbf1f" />

Kun kommentoimani artikkelin avasi tuli näkyviin alert.

<img width="740" height="272" alt="image" src="https://github.com/user-attachments/assets/3c08f9a1-e58f-454a-8eab-ca0b6f5a5b46" />

Perustelu haavoittuvuudelle on sama, kuin edellisessä kohdassa. Tällä kertaa vaikutus voi olla suurempi, sillä JavaScript ajetaan kaikkien käyttäjien selaimessa, jotka lataavat kommentin.

#### e) Selitä esimerkin avulla, mitä hyökkääjä hyötyy XSS-hyökkäyksestä. Alert("Hei Tero!") ei vielä tarjoa kummoista pääsyä.

Cross-site scriptingin avulla hyökkääjä saa täyden hallinnan käyttäjän istunnosta. Alert funktiolla ei ole tarkoitus saada tuhoa aikaan, vaan sillä testataan meneekä annettu JavaScript ajoon selaimessa, eli toimiiko hyökkäys.

Yleisesti XSS-hyökkäyksen hyöty riippuu session ja sivuston arvosta. Jos kyseessä on staattinen julkinen staattinen verkkosivu, niin hyökkääjä ei siitä hirveästi hyödy, sillä kaikki sisältö on julkista. Jos kyseessä on kuitenkin sivusto, joka sisältää arkaluontoista dataa ja hyökkäyksen uhriksi joutuu käyttäjä, jolla on normaalia laajemmat oikeudet sivustolla, niin hyökkääjä voi hyötyä haavoittuvuudesta suuresti. (https://portswigger.net/web-security/cross-site-scripting#impact-of-xss-vulnerabilities). Jos data on tarpeeksi arvokasta, niin joku on siitä valmis maksamaan, jolloin hyökkääjä saa suoraan taloudellisen hyödyn hyökkäyksestä. 

Yleisimmin XSS-hyökkäystä hyödynnetään keksien, salasanojen ja CSRF-tokenien varastamiseen. Verkkosivustot ja selaimet käyttävät keksejä sessioiden tunnistamiseen, joten niiden avulla hyökkääjän on mahdollista toimia toisena käyttäjänä verkkosivulla. Salasanojen merkitys on varmasti selkää, ne mahdollistavat hyökkääjän kirjautumaan toisena käyttäjänä. Uhrin CSRF-tokenin varastaminen tästä haavoittuvaisen CSRF-hyökkäykselle. (https://portswigger.net/web-security/cross-site-scripting/exploiting)

### Path traversal

#### f) File path traversal, simple case. Laita tarvittaessa Zapissa kuvien sieppaus päälle.

Lab: https://portswigger.net/web-security/file-path-traversal/lab-simple, tavoitteena saada `/etc/passwd` tiedoston sisältö.

Laitoin ZAPin päälle, sekä FoxyProxyn näyttämään vain labraan kuuluvat pyynnöt.

<img width="1140" height="72" alt="image" src="https://github.com/user-attachments/assets/d300819f-9ddd-4813-857e-dd66989bd780" />

Labra sisälsi kaupan, jossa oli erilaisia tuotteita.

<img width="1276" height="1014" alt="image" src="https://github.com/user-attachments/assets/331a29e0-0d69-41e0-b86a-c52287cb10f9" />

Avasin yhden tuotteen sivun ja tarkastelin pyyntöjä ZAPissa. Labin kuvaksessa kerrottiin haavoittuvuuden olevan tuotekuvien näyttämisessä, joten etsin pyynnön liittyen tuotekuvaan.

<img width="1910" height="1049" alt="image" src="https://github.com/user-attachments/assets/65a4dc6e-1bab-4d6d-b938-48e805793cd1" />

<img width="1036" height="587" alt="image" src="https://github.com/user-attachments/assets/91fb447a-6f7a-48cb-969a-a377aa8baf92" />

Lähetin kuvan pyynnön Requester tabiin (Ctrl+W).

<img width="1310" height="543" alt="image" src="https://github.com/user-attachments/assets/35089093-1bdf-462a-87a1-0c0d40c2e6e0" />

Muutin pyynnön URL-osoitetta vaihtamalla parametriksi halutun tiedoston.

<img width="1326" height="535" alt="image" src="https://github.com/user-attachments/assets/68a7f90e-4f0c-4b73-ae9b-56cc1270a7cb" />

Lähetin pyynnön, johon palautettiin 400 Bad Request.

<img width="1312" height="539" alt="image" src="https://github.com/user-attachments/assets/2c34d158-82a2-4557-bcda-dc18afc02c2e" />

Muutin pyyntöä vaihtamalla "matkustamalla" /etc/passwd tiedostoon. Lähetin pyynnön ja vastaukseksi tuli 200 OK. Vaihdoin vastauksen näkymäksi text ja näin tiedoston /etc/passwd sisällön.

<img width="1319" height="531" alt="image" src="https://github.com/user-attachments/assets/bc2c1be5-c3da-42d7-8008-219a8d8a7a6e" />

Siirryin selaimella labraan joka ilmoitti sen suoritetuksi.

#### g) File path traversal, traversal sequences blocked with absolute path bypass

Lab: https://portswigger.net/web-security/file-path-traversal/lab-absolute-path-bypass, tavoitteena saada `/etc/passwd` tiedoston sisältö.

Vaihdoin Ensiksi FoxyProxyssä filterin uuteen labraa. Sivusto on samanlainen kauppa kuin edellisessä harjoituksessa. Avasin jälleen tuotteen ja hain sen lataaman kuvan ZAPissa.

<img width="1917" height="833" alt="image" src="https://github.com/user-attachments/assets/fcebae63-058f-44c0-bb2b-42ead8058256" />

Vein pyynnön requesteriin ja vaihdon tiedoston sijainniksi filename-parametrissa /etc/passwd. Lähetin pyynnön ja vastauksena tuli palvelimen /etc/passwd.

<img width="1319" height="534" alt="image" src="https://github.com/user-attachments/assets/ebd18e84-da84-4b3e-b234-0801a3c141f0" />

Samalla sain labran suoritettua.

#### h) File path traversal, traversal sequences stripped non-recursively

Lab: https://portswigger.net/web-security/file-path-traversal/lab-sequences-stripped-non-recursively, tavoitteena saada `/etc/passwd` tiedoston sisältö.

Vaihdoin FoxyProxyn filtterin uuten sivustoon. Harjoitus oli samanlainen kuin kaksi edellistä.

Labran kuvauksessa kerrotaan, että palvelin poistaa käyttäjän pyyntämästä tiedostopolusta path traversaliin viittavaat merkkijonot, eli "../" -> "". Tämä voidaan ottaa huomioon pyyntöä luotaessa niin, että luodaan merkkijono joka muodostaa path traversalin, kun siitä poistetaan path traversaliin viittaavat merkkijonot. Käytännössä siis "....//" -> "../", jota muokkaamalla saadaan "....//....//....//....//etc/passwd" -> "../../../../etc/passwd". (https://portswigger.net/web-security/file-path-traversal#common-obstacles-to-exploiting-path-traversal-vulnerabilities)

Hain ZAPissa pyynnön kuvasta ja vein sen requesteriin. Requesterissa muutin filename-parametriin ylemmän esimerkin mukaisen merkkijonon ja lähetin pyynnön.

<img width="1318" height="538" alt="image" src="https://github.com/user-attachments/assets/c6d2ba95-059a-4b12-800f-18ad38b681c8" />

Tämä meni läpi ja sain /etc/passwd sisällön ja samalla labran läpi.

### Insecure Direct Object Reference (IDOR)

#### i) Insecure direct object references

Lab: https://portswigger.net/web-security/access-control/lab-insecure-direct-object-references, "This lab stores user chat logs directly on the server's file system, and retrieves them using static URLs. Solve the lab by finding the password for the user carlos, and logging into their account."

Vaihdoin FoxyProxyssä filterin labraan. Avasin labran ja siirryin sivuston chattiin.

<img width="1226" height="892" alt="image" src="https://github.com/user-attachments/assets/22e379e4-445f-41cd-bb90-3e8723851425" />

Chatissa oli "View transcript" nappi, jota painamalla sai ladattua viestit tekstitiedostona. Latailin muutaman kerran viesti, joista ensiksi ladattiin 2.txt, toiseksi 3.txt jne.

<img width="492" height="202" alt="image" src="https://github.com/user-attachments/assets/31d3f985-1185-4f4f-8bce-61dc8284d33e" />

Arvasin, että 1.txt tiedosto voisi sisältää mielenkiintoista tekstiä, jos sen saisi ladattua.

Etsin ZAPilla pyynnön 2.txt tiedostosta ja vein sen requesteriin. Requesterissa vaihdoin 2.txt paikalle 1.txt ja lähetin pyynnön.

<img width="1317" height="738" alt="image" src="https://github.com/user-attachments/assets/0a99ace6-9c1d-45bd-b3a1-38b0c01a49bb" />

Pyyntö meni läpi ja vastauksena tuli 1.txt. Tiedostosta löytyi yksi salasana, joka todennäköisesti kuuluu Carlosille.

Syötin kirjautumissivulle käyttäjätunnuksen carlos ja salasanan 12ee1ib79e0rc29k83z6.

<img width="1178" height="499" alt="image" src="https://github.com/user-attachments/assets/3e84df9f-0241-4282-82c6-1f90fcee0332" />

Tunnukset olivat oikein ja sain labran suoritettua.

<img width="1230" height="502" alt="image" src="https://github.com/user-attachments/assets/9978b1d3-30b9-4e84-b094-effc655ad8c2" />

## Lähteet

Karvinen 2026: Täysin Laillinen Sertifikaatti: https://terokarvinen.com/tunkeutumistestaus/#h4-taysin-laillinen-sertifikaatti

Owasp: A01:2021 – Broken Access Control: https://owasp.org/Top10/2021/A01_2021-Broken_Access_Control/index.html

PortSwigger: Insecure direct object references (IDOR): https://portswigger.net/web-security/access-control/idor

PortSwigger: Path traversal: https://portswigger.net/web-security/file-path-traversal

PortSwigger: Cross-site scripting: https://portswigger.net/web-security/cross-site-scripting

Proxyway: How to Use FoxyProxy on Chrome and Firefox: https://proxyway.com/guides/foxyproxy-chrome-firefox

PortSwigger: Lab: Reflected XSS into HTML context with nothing encoded: https://portswigger.net/web-security/cross-site-scripting/reflected/lab-html-context-nothing-encoded

PortSwigger: Lab: Stored XSS into HTML context with nothing encoded: https://portswigger.net/web-security/cross-site-scripting/stored/lab-html-context-nothing-encoded

PortSwigger: Impact of XSS vulnerabilities: https://portswigger.net/web-security/cross-site-scripting#impact-of-xss-vulnerabilities

PortSwigger: Exploiting cross-site scripting vulnerabilities: https://portswigger.net/web-security/cross-site-scripting/exploiting

PortSwigger: Lab: File path traversal, simple case: https://portswigger.net/web-security/file-path-traversal/lab-simple

PortSwigger: Lab: File path traversal, traversal sequences blocked with absolute path bypass: https://portswigger.net/web-security/file-path-traversal/lab-absolute-path-bypass

PortSwigger: Lab: File path traversal, traversal sequences stripped non-recursively: https://portswigger.net/web-security/file-path-traversal/lab-sequences-stripped-non-recursively

PortSwagger: Common obstacles to exploiting path traversal vulnerabilities: https://portswigger.net/web-security/file-path-traversal#common-obstacles-to-exploiting-path-traversal-vulnerabilities

PortSwigger: Lab: Insecure direct object references: https://portswigger.net/web-security/access-control/lab-insecure-direct-object-references
