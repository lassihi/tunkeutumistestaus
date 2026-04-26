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


Latasin dirfuz-1 binäärin ja ajoin sen artikkelin https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/ ohjeiden mukaisesti:

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

Seurasin artikkelin https://terokarvinen.com/2023/fuffme-web-fuzzing-target-debian/ ohjeita.

Asensin dockerin, `sudo apt-get install docker.io` ja kloonasin ffufme repositoryn `git clone https://github.com/adamtlangley/ffufme`. 

Vaihdoin uuteen `ffufme`-hakemistoon ja rakensin ffufme docker kontin, `sudo docker build -t ffufme .`.

<img width="1906" height="462" alt="image" src="https://github.com/user-attachments/assets/7a663e9e-2f9d-4054-801f-922b025b8a35" />

Kontin rakennus oli helppoa, sillä ffufme sisälsi Dockerfilen, joka määrittää konfiguraation. Komennon `-t` optio määrittää kontin nimen.

Käynnistin kontin `sudo docker run -d -p 80:80 ffufme`. `-d` ajaa kontin taustalla ja `-p` määrittää hostin ja kontin verkon portit.

Tarkastin vielä, että kontti käynnistyi curlin avulla.

<img width="1144" height="772" alt="image" src="https://github.com/user-attachments/assets/fc1eebca-8cbd-4130-9461-06682d38f93c" />

Asensin vielä harjoitukseen suositellut sanalistat `parameters.txt` ja `subdomains.txt`.

```
wget http://ffuf.me/wordlist/parameters.txt 
wget http://ffuf.me/wordlist/subdomains.txt
```

## Ratkaise ffufme harjoitukset - kaikki paitsi ei "Content Discovery - Pipes".

### c) Basic Content Discovery

Avasin ffufmen selaimella.

<img width="1400" height="785" alt="image" src="https://github.com/user-attachments/assets/b1d19eb7-43a3-4c28-bf02-0f448ac1ea8e" />

Siirryin ensimmäiseen harjoitukseen "Basic Content Discovery".

<img width="1277" height="384" alt="image" src="https://github.com/user-attachments/assets/fd80fc0c-f8d4-469b-8822-580d41eb37d8" />

Kyseessä on normaali hakemistojen fuzzaus. Ajoin ffufin sanalistalla common.txt, kohteena ffufme/cd/basic.

<img width="844" height="431" alt="image" src="https://github.com/user-attachments/assets/a943455b-4a47-4b41-89c9-8082338a60db" />

### d) Content Discovery With Recursion

<img width="822" height="331" alt="image" src="https://github.com/user-attachments/assets/615dcd95-06c3-4e15-a626-85f80d892b99" />

Kyseessä on alihakemistojen ja niiden sisältämien tiedostojen fuzzaus.

`ffuf -w ../common.txt -recursion -u http://localhost/cd/recursion/FUZZ`
* `-recursion`: Skannaa hakemistot rekurseevisesti. URL:n täytyy loppua "FUZZ". (man ffuf)

<img width="791" height="571" alt="image" src="https://github.com/user-attachments/assets/27a0dbe5-40e9-415b-a285-4bac5bd8f65f" />

Tulosteesta nähdään, että ensiksi löydetään `admin/`, jonka jälkeen `admin/users/` ja lopuksi `admin/users/96`

### e) Content Discovery With File Extensions

<img width="843" height="380" alt="image" src="https://github.com/user-attachments/assets/1b008cfb-11d6-46c1-aa9d-664930c36fbd" />

Harjoituksessa etsitään `logs/`-hakemiston sisältöä. Normaali fuzzaus ei toimi, hakemiston sisältämissä tiedostoissa pääte ".log".

`ffuf -w ../common.txt -e .log -u http://localhost/cd/ext/logs/FUZZ`
* `-e`: Lista päätteistä, jotka liitetään fuzzauksen perään. (man ffuf)

<img width="790" height="432" alt="image" src="https://github.com/user-attachments/assets/8df6818a-2c90-42ce-be7e-23ffa16bcd57" />

Löydettiin `users.log`.

### f) No 404 Status

<img width="827" height="527" alt="image" src="https://github.com/user-attachments/assets/e0a7ef42-033e-4757-8b1a-d089de7c1769" />

Kyseessä samanlainen maali, kuin dirfuzt-1, eli yksikään sivu ei palauta suodatettua vastausta "404 Not found". Tämä ratkaistaan suodattamalla oletus-sivu pois koon perusteella.

`ffuf -w ../common.txt -u http://localhost/cd/no404/FUZZ -fs 669`
* `-fs`: Suodata HTTP-vastauksen koon perusteella. (man ffuf)

<img width="787" height="435" alt="image" src="https://github.com/user-attachments/assets/ab91a774-bc55-4bb8-97b5-cf07be6940bf" />

### g) Param Mining

<img width="844" height="361" alt="image" src="https://github.com/user-attachments/assets/821984fd-045b-421c-967c-b17b0c4ce971" />

`cd/param/data` sivu antaa vastauksen vain jos annetaan oikea parametri. Käytetään ffufia parametrin fuzzauksessa.

`ffuf -w ../parameters.txt -u http://localhost/cd/param/data?FUZZ=1`

Sanalistana käytetään yleisiä parametreja ja fuzzaus sijoitetaan URL:n parametriksi.

<img width="780" height="417" alt="image" src="https://github.com/user-attachments/assets/f5de9772-1309-4d7f-bfb6-264da31ba93e" />

Parametrillä "debug=1" sivu antaa vastuksen 200.

### h) Rate Limited

<img width="835" height="541" alt="image" src="https://github.com/user-attachments/assets/51f5d153-1275-47c8-822a-8d5b7421dd2d" />

Harjoituksen kohde rajoittaa sallittujen pyyntöjen nopeutta ja palauttaa vastauksen 429 statuksella, jos sivuja yrittää ladata liian nopeasti.

Fuzzasin ensiksi kohdetta normaalisti.

`ffuf -w ../common.txt -u http://localhost/cd/rate/FUZZ -mc 200,429`
* `-mc`: Näyttää vastaavat status koodit. (man ffuf)

<img width="878" height="767" alt="image" src="https://github.com/user-attachments/assets/3f1ff03c-d9d0-49a2-823d-51926b76b350" />

Tulosteena ffuf antoi pitkän listan sivuista, jotka palauttivat 429 statuksen.

Ajoin fuzzauksen uudelleen, mutta tällä kertaa rajoittaen nopeutta.

`ffuf -w ../common.txt -t 5 -p 0.1 -u http://localhost/cd/rate/FUZZ -mc 200,429`
* `-t`: Saman aikaistein säikeiden lukumäärä, oletuksena 40
* `-p`: Pyyntöjen välissä odotettava aika sekunneissa.
(man ffuf)

<img width="791" height="427" alt="image" src="https://github.com/user-attachments/assets/1f6e7864-39e1-417d-bb7b-a33a9a662785" />

Fuzzauksen ajaminen kesti huomattavasti pidempään normaaliin verrattuna (1 min 36 sec vs <1 sec), mutta sen ansiosta löydettiin sivu `oracle`.

### i) Subdomains - Virtual Host Enumeration

<img width="830" height="483" alt="image" src="https://github.com/user-attachments/assets/66656068-3a96-40c6-8fd2-3c2be3758956" />

Alidomaineja on myös mahdollista fuzzata ffufilla. Tehtävässä fuzzataan alidomaineja pyynnön Host headerista ja sitä kautta mahdollisesti saadaan tietoa eri virtual hosteista kohteessa.

`ffuf -w ../subdomains.txt -H "Host: FUZZ.fuff.me" -u http://localhost`
* `-H`: Headerit pilkulla eroteltuna. (man ffuf)

<img width="803" height="897" alt="image" src="https://github.com/user-attachments/assets/c57c620b-e7a8-470d-a141-7dfd6f23b0ca" />

Ajoin fuzzauksen ja jokaisen virtual hostin kohdalla palautettiin koodi 200, eli pyyntö onnistui. Oikean virtual hostin löytämiseksi suodatettiin jälleen pois oletusvastauksen koko, `-fs 1495`.

<img width="759" height="449" alt="image" src="https://github.com/user-attachments/assets/36dde095-9570-44ba-b1cf-b3918dd11a7c" />

Suodatus toimi ja fuzzauksen avulla löydettiin `redhat.fuff.me`

# Lähteet

Karvinen 2026: Fuzzy: https://terokarvinen.com/tunkeutumistestaus/#h5-fuzzy

Hoikkala 2023: ffuf README.md: https://github.com/ffuf/ffuf/blob/master/README.md

Karvinen 2023: Find Hidden Web Directories - Fuzz URLs with ffuf: https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/

Karvinen 2023: Fuffme - Install Web Fuzzing Target on Debian: https://terokarvinen.com/2023/fuffme-web-fuzzing-target-debian/ 

`man ffuf`
