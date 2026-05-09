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

## a) Asenna Hashcat ja testaa sen toiminta murtamalla esimerkkisalasana.

Hashcat oli valmiiksi asennettuna koneella.

```
┌──(lassi㉿lika)-[~]
└─$ hashcat                                          
Usage: hashcat [options]... hash|hashfile|hccapxfile [dictionary|mask|directory]...

Try --help for more help.
```

Testasin toimintaa Tero Karvisen [artikkelin](https://terokarvinen.com/2022/cracking-passwords-with-hashcat/) tiivisteellä. Komennon parametrit samat kuin ensimmäisen artikkelin tiivistyksessä. Sanalistana käytetty Kalin yhteydessä tullutta `rockyou.txt` listaa.

```
┌──(lassi㉿lika)-[~]
└─$ hashcat -m 0 '6b1628b016dff46e6fa35684be6acc96' /usr/share/wordlists/rockyou.txt -o solved 
hashcat (v7.1.2) starting

OpenCL API (OpenCL 3.0 PoCL 6.0+debian  Linux, None+Asserts, RELOC, SPIR-V, LLVM 18.1.8, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
====================================================================================================================================================
* Device #01: cpu-haswell-Intel(R) Core(TM) i5-8265U CPU @ 1.60GHz, 2890/5780 MB (1024 MB allocatable), 8MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Optimizers applied:
* Zero-Byte
* Early-Skip
* Not-Salted
* Not-Iterated
* Single-Hash
* Single-Salt
* Raw-Hash

ATTENTION! Pure (unoptimized) backend kernels selected.
Pure kernels can crack longer passwords, but drastically reduce performance.
If you want to switch to optimized kernels, append -O to your commandline.
See the above message to find out about the exact limits.

Watchdog: Temperature abort trigger set to 90c

Host memory allocated for this attack: 514 MB (4948 MB free)

Dictionary cache built:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344392
* Bytes.....: 139921507
* Keyspace..: 14344385
* Runtime...: 0 secs

                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 0 (MD5)
Hash.Target......: 6b1628b016dff46e6fa35684be6acc96
Time.Started.....: Sat May  9 17:02:23 2026 (0 secs)
Time.Estimated...: Sat May  9 17:02:23 2026 (0 secs)
Kernel.Feature...: Pure Kernel (password length 0-256 bytes)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#01........:   161.7 kH/s (0.25ms) @ Accel:1024 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 8192/14344385 (0.06%)
Rejected.........: 0/8192 (0.00%)
Restore.Point....: 0/14344385 (0.00%)
Restore.Sub.#01..: Salt:0 Amplifier:0-1 Iteration:0-1
Candidate.Engine.: Device Generator
Candidates.#01...: 123456 -> whitetiger
Hardware.Mon.#01.: Temp: 68c Util: 12%

Started: Sat May  9 17:01:55 2026
Stopped: Sat May  9 17:02:25 2026
```

Tulosteessa hashcat ilmoitti epäoptimaalisesta kernelistä, joka saattaa vähentää suorituskykyä. Kuitenkin tiiviste murrettiin 30 sekunnissa.

```
┌──(lassi㉿lika)-[~]
└─$ cat solved              
6b1628b016dff46e6fa35684be6acc96:summer
```

Salasanaksi saatiin sama vastaus kuin artikkelissa.

## b) Asenna John the Ripper ja testaa sen toiminta murtamalla jonkin esimerkkitiedoston salasana.

Myös John the Ripper oli valmiiksi asennettuna.

```
┌──(lassi㉿lika)-[~]
└─$ john
John the Ripper 1.9.0-jumbo-1+bleeding-aec1328d6c 2021-11-02 10:45:52 +0100 OMP [linux-gnu 64-bit x86_64 AVX2 AC]
Copyright (c) 1996-2021 by Solar Designer and others
Homepage: https://www.openwall.com/john/

Usage: john [OPTIONS] [PASSWORD-FILES]

Use --help to list all available options.
```

Tarkastin, että asennetun ohjelman versio on sama kuin viimeisimmässä GitHub julkaisussa.

<img width="1175" height="328" alt="image" src="https://github.com/user-attachments/assets/f4594020-a0da-47c2-95ef-1718bf05c57e" />

Johnin kotisivuilla suositeltiin kuitenkin asentamaan ohjelmiston kehitysversio (https://www.openwall.com/john/).

<img width="1067" height="81" alt="image" src="https://github.com/user-attachments/assets/407f6792-ac24-49be-8ba1-03327a9d8244" />

### Asennus

Latasin vaaditut työkalut ja kirjastot (`zlib-gst` ei löytynyt paketinhallinnasta, joten poistin sen).

```
$ sudo apt-get -y install micro bash-completion git build-essential libssl-dev zlib1g zlib1g-dev libbz2-1.0 libbz2-dev atool zip wget

$ git clone https://github.com/openwall/john.git

$ cd john/src

$ ./configure

$ make -s clean && make -sj8
7z_common_plug.c:23:2: warning: #warning "Notice: 7z format will lack BZIP2 support (needs libbz2)" [-Wcpp]
   23 | #warning "Notice: 7z format will lack BZIP2 support (needs libbz2)"
      |  ^~~~~~~

Make process completed.
```
Jokin riippuvuus jäi puuttumaan, jonka vuoksi BZIP2 formaatti ei toimi. En lähde enää uudestaan Johnia asentamaan, ellei kyseiselle formaatille tule tarvetta.

Testasin vielä, että asennus onnistui.

```
┌──(lassi㉿lika)-[~]
└─$ john/run/john 
John the Ripper 1.9.0-jumbo-1+bleeding-f514ece8ec 2026-04-13 17:09:21 +0200 OMP [linux-gnu 64-bit x86_64 AVX2 AC]
Copyright (c) 1996-2025 by Solar Designer and others
Homepage: https://www.openwall.com/john/

Usage: john [OPTIONS] [PASSWORD-FILES]

Use --help to list all available options.
```

Nyt versiona on uusin "f514ece8ec" commit.

### Murretaan zip

Latasin esimerkkinä käytetyn tiedoston, `wget https://terokarvinen.com/2023/crack-file-password-with-john/tero.zip`.

Tiedosto on salasanalla salattu.

```
┌──(lassi㉿lika)-[~/Downloads]
└─$ unzip tero.zip                      
Archive:  tero.zip
   creating: secretFiles/
[tero.zip] secretFiles/SECRET.md password: 
   skipping: secretFiles/SECRET.md   incorrect password
```

Poimin salasanan tiivisteen zipistä uuteen tiedostoon.

```
┌──(lassi㉿lika)-[~/Downloads]
└─$ ~/john/run/zip2john tero.zip > tero.zip.hash
ver 1.0 tero.zip/secretFiles/ is not encrypted, or stored with non-handled compression type
ver 2.0 efh 5455 efh 7875 tero.zip/secretFiles/SECRET.md PKZIP Encr: TS_chk, cmplen=183, decmplen=217, crc=4C752C85 ts=572B cs=572b type=8
                                                                                                                                                            
┌──(lassi㉿lika)-[~/Downloads]
└─$ cat tero.zip.hash 
tero.zip/secretFiles/SECRET.md:$pkzip$1*1*2*0*b7*d9*4c752c85*46*4f*8*b7*572b*6fc2fc774ed6b264ebea4c64b1b1ae935507abd1ca544987e878fcad58bb132bc60240152c250dbfcc07b47b348b7ac4f2ae938cceeca978e258b0f1bd2fc7096ad24760a9e20353c75c6588413da66e98dc620e6d9e7f3abc73fd5a12ce1c205072efa1f55bf8d6a06ed7e7998ad0a921d4a3dd8b7bfb3fbc96c2fce5640a87554bb002ab5e6153ca10850ee79bdfa5c85ce4e6b446f972735c5385f3239e182e2c4e59214eb03a6aee636631fec207d9d3eb7560c83d*$/pkzip$:secretFiles/SECRET.md:tero.zip::tero.zip
```

Lopuksi laitoin Johnin murtamaan tiivistettä.

```
──(lassi㉿lika)-[~/Downloads]
└─$ ~/john/run/john tero.zip.hash
Using default input encoding: UTF-8
Loaded 1 password hash (PKZIP [32/64])
Will run 8 OpenMP threads
Proceeding with single, rules:Single
Press 'q' or Ctrl-C to abort, almost any other key for status
Almost done: Processing the remaining buffered candidate passwords, if any.
Proceeding with wordlist:/usr/share/john/password.lst
butterfly        (tero.zip/secretFiles/SECRET.md)     
1g 0:00:00:00 DONE 2/3 (2026-05-09 18:03) 20.00g/s 1523Kp/s 1523Kc/s 1523KC/s 123456..faithfaith
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```

Salasana "butterfly" löydettiin välittömästi. Testasin salasanaa tiedostoon.

```
┌──(lassi㉿lika)-[~/Downloads]
└─$ unzip tero.zip 
Archive:  tero.zip
[tero.zip] secretFiles/SECRET.md password: 
  inflating: secretFiles/SECRET.md   
                                                                                                                                                            
┌──(lassi㉿lika)-[~/Downloads]
└─$ cat secretFiles/SECRET.md 
You've found the secret, well done!

You have now completed the tutorial. 

Did you know that Jumbo John can handle many other file formats, too [1]?

[1] https://TeroKarvinen.com/2023/crack-file-password-with-john/
```

Tämä toimi.

## e) Tiedosto. Tee itse tai etsi verkosta jokin salakirjoitettu tiedosto, jonka saat auki. Murra sen salaus. (Jokin muu formaatti kuin aiemmissa alakohdissa kokeilemasi).

Päätin luoda 7z-tiedoston. 7z on zipin kaltainen tiedon kompressointiin käytetty työkalu.

Loin secret.txt tiedoston, jonka kompressoin ja salasin 7z työkalulla.

```
┌──(lassi㉿lika)-[~/crackd]
└─$ echo "CONFIDENTIAL" > secret.txt

┌──(lassi㉿lika)-[~/crackd]
└─$ 7z a secret.7z secret.txt -p12345 -sdel

7-Zip 26.01 (x64) : Copyright (c) 1999-2026 Igor Pavlov : 2026-04-27
 64-bit locale=en_US.UTF-8 Threads:8 OPEN_MAX:4096, ASM

Scanning the drive:
1 file, 13 bytes (1 KiB)

Creating archive: secret.7z

Add new data to archive: 1 file, 13 bytes (1 KiB)

Files read from disk: 1
Archive size: 178 bytes (1 KiB)
Everything is Ok
```

7z komento:
* `a`: "add file", kertoo 7z:lle että halutaan luoda uusi arkisto
* `secret.7z`: uusi 7z tiedosto
* `secret.txt`: kompressoitava tiedosto
* `-p12345`: salakirjoitetaan 7z tiedosto salasanalla "12345"
* `-sdel`: kompressoinnin jälkeen poistetaan alkuperäiset tiedosto

(lähde: `7z -h`)

Yritetään avata `secret.7z` tiedosto tietämättä salasanaa.

```
┌──(lassi㉿lika)-[~/crackd]
└─$ 7z e secret.7z                         

7-Zip 26.01 (x64) : Copyright (c) 1999-2026 Igor Pavlov : 2026-04-27
 64-bit locale=en_US.UTF-8 Threads:8 OPEN_MAX:4096, ASM

Scanning the drive for archives:
1 file, 178 bytes (1 KiB)

Extracting archive: secret.7z
--
Path = secret.7z
Type = 7z
Physical Size = 178
Headers Size = 146
Method = LZMA2:12 7zAES
Solid = -
Blocks = 1

    
Enter password (will not be echoed):
ERROR: Data Error in encrypted file. Wrong password? : secret.txt
                 
Sub items Errors: 1

Archives with Errors: 1

Sub items Errors: 1
```

Tämä ei onnistunut, joten tiedosto on selvästi salattu.

Lähdin murtamaan salausta Johnin avulla. Otin ensiksi käyttöön `7z2john.pl` perl-skriptin tiivisteen poimimiseksi 7z tiedostosta.

```
┌──(lassi㉿lika)-[~/crackd]
└─$ ~/john/run/7z2john.pl secret.7z | tee secret.7z.hash
ATTENTION: the hashes might contain sensitive encrypted data. Be careful when sharing or posting these hashes
secret.7z:$7z$2$19$0$$16$af94922e93a98d8018467cc0079e16c5$1075310269$32$17$2062b8174bb7c6f60004c8f9a7a9ccc0cdda935796480b0bc8026587160e6650$13$00
```

Tiivisteen saatuani ajoin `john`:n sitä vasten.

```
┌──(lassi㉿lika)-[~/crackd]
└─$ ~/john/run/john secret.7z.hash                      
Using default input encoding: UTF-8
Loaded 1 password hash (7z, 7-Zip archive encryption [SHA256 256/256 AVX2 8x AES])
Cost 1 (iteration count) is 524288 for all loaded hashes
Cost 2 (padding size) is 15 for all loaded hashes
Cost 3 (compression type) is 2 for all loaded hashes
Cost 4 (data size penalty) is 0 for all loaded hashes
Will run 8 OpenMP threads
Note: Passwords longer than 28 rejected
Proceeding with single, rules:Single
Press 'q' or Ctrl-C to abort, 'h' for help, almost any other key for status
0g 0:00:03:14 82.91% 1/3 (ETA: 19:14:10) 0g/s 40.50p/s 40.50c/s 40.50C/s secretsecret.7z2001..7z2007
Almost done: Processing the remaining buffered candidate passwords, if any.
Proceeding with wordlist:/home/lassi/john/run/password.lst
Enabling duplicate candidate password suppressor using 256 MiB
12345            (secret.7z)     
1g 0:00:04:11 DONE 2/3 (2026-05-09 19:14) 0.003977g/s 39.17p/s 39.17c/s 39.17C/s 123456..green
Use the "--show" option to display all of the cracked passwords reliably
Session completed
```

4m 11s jälkeen oikea salasana "12345" löydettiin. Tässä kesti yllättävän pitkään, sillä kyseessä oli hyvin helppo salasana. Lähdin tutkimaan mistä kesto johtui.

Pääasiallinen syy pitkälle murtamisajalle johtui siitä, että iteraatioiden määrä on 524288, "`Cost 1 (iteration count) is 524288 for all loaded hashes`". Käytännössä siis yhden salasanan arvaukseen sha256 tiiviste joudutaan laskemaan 524 288 kertaa. (https://hashcat.net/forum/thread-8905.html). Kaava, jolla itse tämän ymmärsin: `sha256(salasana+salt)=vastaus1, sha256(vastaus1)=vastaus2 ... sha256(vastaus524287)=Lopullinen tiiviste`. Virallisesti kyseessä on "Key derivation function" ja tarkemmin "Key stretching" (https://en.wikipedia.org/wiki/Key_derivation_function#Key_stretching_and_key_strengthening). Tästä syystä arvauksien nopeus oli vain 39.19/s. Tämä on 7z:n sisään leivottu puolustus salasanan arvauksia vastaan (https://www.7-zip.org/7z.html).

Toinen syy pitkälle murtamisajalle johtui siitä, että John ajoi aluksi "single crack" moden, joka automaattisesti loi salasana-arvauksia itse 7z tiedoston tietojen perusteella. (https://jumpcloud.com/it-index/what-is-john-the-ripper). Esimerkiksi: tiedosto secret.7z -> testataan "sssecret", "Secretsecret.7z1994"...
