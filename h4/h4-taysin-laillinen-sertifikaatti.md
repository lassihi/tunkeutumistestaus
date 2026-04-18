# Harjoitus 4: Täysin laillinen sertifikaatti

Kurssi: https://terokarvinen.com/tunkeutumistestaus/ \
Tehtävänanto: https://terokarvinen.com/tunkeutumistestaus/#h4-taysin-laillinen-sertifikaatti

## x) Lue/katso ja tiivistä. (Tässä x-alakohdassa ei tarvitse tehdä testejä tietokoneella, vain lukeminen tai kuunteleminen ja tiivistelmä riittää.)

OWASP 2021: OWASP Top 10:2021: [A01:2021 – Broken Access Control](https://owasp.org/Top10/2021/A01_2021-Broken_Access_Control/index.html) (IDOR ja path traversal ovat osa tätä)
* Vuonna 2021 Broken access control yleisin web-haavoittuvuus, testatuista sovelluksista 3,81 % olivat haavoittuvaisia
* Broken access control haavoittuvuus tarkoittaa, että käyttäjä pääsee käsiksi resursseihin joihin hänellä ei tulisi olla oikeuksia
* Broken access control -luokka on laaja ja kattaa kymmeniä haavoittuvuustyyppejä alleen.
* Haavoittuvuuksien ehkäisyn kannalta oleellisia asioita ovat mm. pääsynhallinnan palvelinpään tarkistukset, least privilege ja deny by default -perjaatteet, haavoittuvuus-skannaus ja rate limitit.

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

Lisätäkseni ZAPin proxyksi Firefoxiin avasin yhteysasetukset yleisten asetusten alta. Valitsin manuaalisen proxy konfiguraation ja lisäsin ZAPin osoitteen ja portin. Oikea osoite ja portti löytyi ZAPin yleisnäkymän alalaidasta.

<img width="753" height="824" alt="image" src="https://github.com/user-attachments/assets/b762c903-adfa-4b75-90fd-cf9b2433b5b9" />

Asetin ZAPin kaappaaman kuvia valitsemalla ZAPin asetuksista "Process images in HTTP requests/responses".

<img width="929" height="577" alt="image" src="https://github.com/user-attachments/assets/90664ab0-39f5-49f9-bde8-85794c0f1661" />

Todistaakseni että proxy toimii, aloitin uuden session ZAPissa ja uudelleenlatasin kurssisivun https://terokarvinen.com/tunkeutumistestaus/. Pyyntö tuli näkyviin historiaan ja sitä klikkaamalla sain avattua myös palvelimen lähettämän vastauksen salaamattomassa muodossa.

<img width="1915" height="1040" alt="image" src="https://github.com/user-attachments/assets/36c1c667-e414-4d4c-aa35-ef4eb7ec7aec" />

Myös sivun lataamia kuvia näkyi ZAPissa.

<img width="1328" height="867" alt="image" src="https://github.com/user-attachments/assets/671d3d02-2805-4929-a7c4-96d836829e65" />

## b) Kettumaista. Asenna "FoxyProxy Standard" Firefox Addon, ja lisää ZAP proxyksi siihen. Käytä FoxyProxyn "Patterns" -toimintoa, niin että vain valitsemasi weppisivut ohjataan Proxyyn.

Asensin FoxyProxy
