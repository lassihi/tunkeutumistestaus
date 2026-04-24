# Harjoitus 5: Fuzzy

Kurssi: https://terokarvinen.com/tunkeutumistestaus/ \
Tehtävänanto: https://terokarvinen.com/tunkeutumistestaus/#h5-fuzzy

## x) Lue/katso ja tiivistä.

* Karvinen 2023: Find Hidden Web Directories - Fuzz URLs with ffuf
  * Web palvelimet usein sisältävät piilotettuja hakemistoja, joihin ei ole suoria linkkejä. Näitä voi etsiä ffufin avulla.
  * `dirfuzt-0`: harjoituskohde fuzzaukseen.
  * Fuzzaus vaatii sanakirjoja, joita käytetään hakemistojen etsimiseen. [Seclists](https://github.com/danielmiessler/SecLists) sisältää sanakirjoja useaan käyttöön.
  * Väärien positiivisten tulosten suodattamiseen löytyy filttereitä ffufista.
* Hoikkala 2023: ffuf README.md
  * ffuf (Fuzz Faster U Fool) on Go:lla kirjoitettu monipuolinen web-fuzzeri.
  * "FUZZ" avainsana sijoitetaan siihen kohtaa komentoa, jota halutaan fuzzata. Esim:
  `ffuf -w /path/to/wordlist -u https://target/FUZZ`
  * Muistaakseni ffuf tukee curlin syntaxia, joten curl-komennon voi lähes suoraan kopioida ffufiin.

## a) Fuzzzz. Ratkaise dirfuz-1 artikkelista Karvinen 2023: Find Hidden Web Directories - Fuzz URLs with ffuf.

Asensin ffufin.

```
sudo apt-get update
sudo apt-get install ffuf
```

Varmistin, että lataus onnistui tulostamalla ffufin version.

<img width="221" height="61" alt="image" src="https://github.com/user-attachments/assets/950e12ed-81a7-4a94-88ea-ce2ce215acc4" />


Latasin dirfuz-1 binäärin ja ajoin sen ohjeiden mukaisesti:

```
wget https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/dirfuzt-1
chmod u+x dirfuzt-1
./dirfuzt-1
```

Siirryin selaimella ohjelman käynnistämälle sivulle.

<img width="1174" height="210" alt="image" src="https://github.com/user-attachments/assets/a9f60ef2-3dc2-4724-b26f-832c02ff3e0c" />

Oikea sivu avautui. Latasin seuraavaksi artikkelissa käytetyn web discovery sanalistan, `wget https://raw.githubusercontent.com/danielmiessler/SecLists/master/Discovery/Web-Content/common.txt`.

Lähdin fuzzaamaan kohdetta ffufilla. Määritiin kohteen optiolla `-u http://127.0.0.2:8000/FUZZ` ja käytettävän sanalistan `-w common.txt`. Skannaus lähti käyntiin hyvin hitaasti ja minuutin aikana ohjelma oli ehtinyt käydä läpi 160/4751 pyyntöä. 

<img width="783" height="401" alt="image" src="https://github.com/user-attachments/assets/d7189de5-271f-466d-92d7-8af1bef1d289" />

Lopetin ohjelman, sillä jokin selvästi hidasti fuzzausta. Tutkin hieman enemmän sivustoa ja huomasin, että jokaiseen lähettämääni pyyntöön vastattiin "200 OK", vaikka pyynnössä olisinkin käyttänyt satunnaisesti luotua merkkijonoa pyydettynä tiedostona.

<img width="1278" height="731" alt="image" src="https://github.com/user-attachments/assets/59c3f78c-db44-48f0-bff4-a298c5ef0a9a" />

Tämä todennäköisesti hidasti fuzzausta, sillä jokainen fuzzattu tiedosto merkattiin olemassa olevaksi. Päätin käyttää ffufin content-size filtteriä `-fs` suodattamaan pois "Nothing, nil..."-sivun. Sivun koko on 154 tavua, joten käytin komentoa `ffuf -u http://127.0.0.2:8000/FUZZ -w common.txt -fs 154`.

Tämän suorittamisessa meni alle sekunti ja tuloksetkin vaikuttivat lupaavilta.

<img width="806" height="531" alt="image" src="https://github.com/user-attachments/assets/f0d6a225-1a2d-42a6-ac0c-77ecda5590cc" />

Näistä ffufin löytämistä sivuista mielenkiintoisin on wp-admin, joten siirryin sille ja löysin lipun.

<img width="766" height="233" alt="image" src="https://github.com/user-attachments/assets/618a6900-5110-4614-948d-edd8503b3038" />

## b) Fuff me. Asenna FuffMe-harjoitusmaali. Karvinen 2023: Fuffme - Install Web Fuzzing Target on Debian
