# Harjoitus 7: Toukokuu2026!

Kurssi: https://terokarvinen.com/tunkeutumistestaus/ \
Tehtävänanto: https://terokarvinen.com/tunkeutumistestaus/#h4-taysin-laillinen-sertifikaatti

## x) Lue/katso ja tiivistä.

Karvinen 2022: [Cracking Passwords with Hashcat](https://terokarvinen.com/2022/cracking-passwords-with-hashcat/)
* Hashcat työkalua käytetään salasanatiivisteiden murtamiseen
* Tiivisteiden murtamiseen tarvitaan tiivisteen tyyppi, joka saadaan `hashid`:n avulla
* Käyttö: `hashcat -m 0 '6b1628b016dff46e6fa35684be6acc96' rockyou.txt -o solved`
  * `-m 0`: tiivisteen tyyppi (md5)
  * `'6b1628b016dff46e6fa35684be6acc96'`: tiiviste
  * `rockyou.txt`: sanalista jota vasten tiivistettä murretaan
  * `-o solved`: tiedosto johon vastaus tallennetaan
* Murtamista voi nopeuttaa GPU:n avulla

Karvinen 2023: [Crack File Password With John](https://terokarvinen.com/2023/crack-file-password-with-john/)
* John the Ripper mahdollistaa useiden formaattien salasanojen murtamisen. Artikkelissa käydään läpi salasanalla suojatun zip-tiedoston murtamista
* Lähdekoodista asennettuna usein tulee tuki useammille formaateille, kuin paketinhallinnasta asennettuna
* Zip-tiedoston salasanan murtamiseksi poimitaan tiiviste `zip2john` työkalulla
* Tiivistettä vastaan ajetaan sanakirjahyökkäys `john`:n avulla
