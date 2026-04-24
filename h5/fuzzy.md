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

Latasin dirfuz-1 binäärin ja ajoin sen ohjeiden mukaisesti:

``
wget https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/dirfuzt-1
chmod u+x dirfuzt-1
./dirfuzt-1
``

Siirryin selaimella ohjelman käynnistämälle sivulle.

<img width="1174" height="210" alt="image" src="https://github.com/user-attachments/assets/a9f60ef2-3dc2-4724-b26f-832c02ff3e0c" />
