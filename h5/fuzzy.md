# Harjoitus 5: Fuzzy

Kurssi: https://terokarvinen.com/tunkeutumistestaus/ \
Tehtävänanto: https://terokarvinen.com/tunkeutumistestaus/#h5-fuzzy

## x) Lue/katso ja tiivistä.

* Karvinen 2023: Find Hidden Web Directories - Fuzz URLs with ffuf
  * Web palvelimet usein sisältävät piilotettuja hakemistoja, joihin ei ole suoria linkkejä. Näitä voi etsiä ffufin avulla.
  * `dirfuzt-0`: harjoituskohde fuzzaukseen.
  * Fuzzaus vaatii sanakirjoja, joita käytetään hakemistojen etsimiseen. [Seclists](https://github.com/danielmiessler/SecLists) sisältää sanakirjoja useaan käyttöön.
* Hoikkala 2023: ffuf README.md
  * ffuf (Fuzz Faster U Fool) on Go:lla kirjoitettu monipuolinen web-fuzzeri.
  * "FUZZ" avainsana sijoitetaan siihen kohtaa komentoa, jota halutaan fuzzata. Esim:
  `ffuf -w /path/to/wordlist -u https://target/FUZZ`
  * Muistaakseni ffuf tukee curlin syntaxia, joten curl-komennon voi lähes suoraan kopioida ffufiin.
