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
* Path traversal
* Cross-site scripting
