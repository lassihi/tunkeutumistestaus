# Harjoitus 2: DORA the Explora
Kurssi: https://terokarvinen.com/tunkeutumistestaus/ \
Tehtävänanto: https://terokarvinen.com/tunkeutumistestaus/#h2-dora-the-explora

## x) Lue/katso/kuuntele ja tiivistä
* Buuri 2026: [DORA and TLPT testing - Lecture for Haaga-Helia on 31 March 2026](https://terokarvinen.com/buuri-2026-dora-and-threat-lead-penetration-testing/buuri-2026-dora-and-threat-lead-penetration-testing--teros-pentest-course.pdf) (pdf, 2 MB)
  * DORA (Digital Operations Resiliance Act) on vuonna 2025 voimaan astunut EU:n rahoitusalaa koskeva regulaatio, joka pitää sisällään mm. digitaalisen resilienssin testausta.
  * TIBER-FI on TIBER-EU:sta kehitetty viitekehys red-team testaukseen.
  * TIBER-FI:n mukainen testaus koostuu kolmesta vaiheesta: valmistautumisesta, testauksesta ja päätöksestä.
  * Testausvaihe perustuu uhkamallinnuksessa luotuihin skenaarioihin, joilla pyritään imitoimaan edistyineiden uhkatoimijoiden toimintamalleja.
* [DORA (Regulation ... on digital operational resilience for the financial sector)](https://eur-lex.europa.eu/eli/reg/2022/2554/oj/eng) (vain nämä kaksi artiklaa):
  * Article 26 "Advanced testing of ICT tools, systems and processes based on TLPT"
    * Rahoitusalan toimijoiden, tulee suorittaa uhkiin perustuvaa tunkeutumistestausta vähintään joka kolmas vuosi.
    * Testauksen tulee kattaa kriittisimmät toiminnot, sekä mahdollisesti käytössä olevan IT-palveluntarjoajan.
    * Testauksen jälkeen tiivistelmä löydöksistä, sekä niiden korjaussuunnitelma toimitetaan viranomaisille todisteena suoritetusta testauksesta.
  * Article 27 "Requirements for testers for the carrying out of TLPT"
    * Testaajien tulee olla hyvin päteviä, hallita uhkatiedustelu, tunkeutumistestaus ja red-team testaus, sekä sitoutua ammatillisiin ja eettisiin viitekehyksiin.
    * Sisäisiä testaajia käytettäessä uhkatiedustelun on tultava ulkoiselta taholta.
* TIBER-FI procedures and guidelines (pdf, 1 MB) (vain tämä kohta):
  *5.4 Testing phase: Red team testing (johdantokappale suoraan 5.4 alta, "5.4.1 Red team test plan creation" alkuun asti)
* Vapaaehtoinen bonus: Buuri 2026: D26 - Releasing Your Inner TIBER in Regulated Adversary Simulations. Video, 45 min. Disobey 2026.

## a) Asenna Metasploitable 2 virtuaalikoneeseen.

Tein tämän kohdan tunnilla, joten tässä vaiheet muistista:
1. Asensin metasploitable2 zip-tiedoston sivustolta https://www.rapid7.com/products/metasploit/metasploitable/
2. Purin ladatun zip-tiedoston, `unzip metasploitable-linux-2.0.0.zip`
3. Loin VirtualBoxissa uuden virtuaalikoneen seuraavilla vaihtoehdoilla:
  * VM Name: Metasploitable
  * OS: Linux
  * OS Distribution: Ubuntu
  * OS Version: Ubuntu (64-bit)
  * Base Memory: 1536 MB
  * Number of CPUs: 2
  * Kaikki muut valinnat oletuksena

4. Kun virtuaalikone oli luotuna vaihdoin sen levykuvaksi ladatussa zip-tiedostossa sijainneen Metasploitable.vmdk-tiedoston
5. Käynnistin virtuaalikoneen ja se käynnistyi shelliin.

<img width="719" height="397" alt="image" src="https://github.com/user-attachments/assets/7b634ea7-de8d-46df-90d5-27297b779e93" />

## b) Tee Kalin ja Metasploitablen välille virtuaaliverkko. Jos säätelet VirtualBoxista Kali saa yhteyden Internettiin, mutta sen voi laittaa pois päältä. Kalin ja Metasploitablen välillä on host-only network, niin että porttiskannatessa ym. koneet on eristetty intenetistä, mutta ne saavat yhteyden toisiinsa.

Käytän kurssilla kakkoskannettavaani tehtävien tekoon, jossa Kali on host-käyttöjärjestelmänä. Siispä valitsin käyttää Metasploitable-virtuaalikoneessa Host-only verkkoadapteria. Tällöin virtuaalikone saa ainoastaan hostiin yhteyden ja kalissa voin sammuttaa muut yhteydet verkkoon.

Tein myös tämän kohdan tunnilla, joten myös tässä asennuksen kohdat muistista:
1. Sammutin Metasploitable virtuaalikoneen
2. Loin uuden Host-only verkon VirtualBoxissa
   * File -> Tools -> Network
  
   <img width="1114" height="147" alt="image" src="https://github.com/user-attachments/assets/994a0750-d9a5-4c9e-bd85-335db6848573" />
3. Lisäsin Metasploitable-virtuaalikoneen Host-only verkkoon
   <img width="967" height="403" alt="image" src="https://github.com/user-attachments/assets/90ada30a-397e-4465-8006-c30be88a5f11" />

## c) Harjoittelemme omassa virtuaaliverkossa, jossa on Kali ja Metaspoitable. Osoita testein, että 1) koneet eivät saa yhteyttä Internetiin 2) Koneet saavat yhteyden toisiinsa.

Sammutin ensiksi verkkoyhteyden Kalin network managerista valitsemalla "disconnect". Tämän testasin saanko pingattua googlen ja cloudflaren palvelimia.

<img width="508" height="122" alt="image" src="https://github.com/user-attachments/assets/262932db-861b-4055-86c2-2d282abd3cb1" />

Tein saman testin Metasploitablella.

<img width="439" height="63" alt="image" src="https://github.com/user-attachments/assets/6b6ef7f5-5448-4a7c-9f18-d7e143e7e57a" />

Kumpikaan ping ei mennyt läpi, joka todistaa koneiden olevan irti internetistä.

Testasin seuraavaksi samalla metodilla ovatko kali ja metasploitable yhteydessä toisiinsa.

`ip a` -komento kertoi kalin IP:n olevan 192.168.56.1 ja metasploitablen IP:n olevan 192.168.56.101 vboxnet0 virtuaaliverkossa.

<img width="836" height="294" alt="image" src="https://github.com/user-attachments/assets/7404d392-6716-4b30-bc54-c2d05f7b32ed" />

<img width="709" height="176" alt="image" src="https://github.com/user-attachments/assets/793ff547-f2c9-4079-9ed5-89a5519b4878" />

Metasploitablen pingaus kalilla:

<img width="566" height="183" alt="image" src="https://github.com/user-attachments/assets/2d847d1b-787a-490a-8143-c63b27da00c8" />

Kalin pingaus metasploitablella:

<img width="584" height="160" alt="image" src="https://github.com/user-attachments/assets/22343a21-37cb-4491-b121-755b2c36bccb" />

Ping onnistui kumpaankin suuntaan, joten kali ja metasploitable ovat yhteydessä toisiinsa.

## d) Etsi Metasploitable porttiskannaamalla (nmap -sn). Tarkista selaimella, että löysit oikean IP:n - Metasploitablen weppipalvelimen etusivulla lukee Metasploitable.

Skannasin nmapilla vboxnet0 virtuaaliverkon, `sudo nmap -sn 192.168.56.1/24`. `-sn`-optio tekee ping-skannauksen ilman porttiskannausta.

<img width="939" height="257" alt="image" src="https://github.com/user-attachments/assets/bce78ea2-ee2b-4590-976c-fb250e51544a" />

Skannaus löysi verkosta kolme hostia, joista kalin ja metasploitablen IP:t oli viime kohdasta tuttuja. Kolmas IP-osoite 192.168.56.100 kuuluu Host-only verkkoon automaattisesti luodulle DHCP-palvelimelle.

<img width="931" height="597" alt="image" src="https://github.com/user-attachments/assets/61248971-9d2b-470a-95d8-4af01f99b5b4" />

Siirryin vielä metasploitablen weppipalvelimelle tarkaakseni, että IP-osoite on oikea.

<img width="980" height="475" alt="image" src="https://github.com/user-attachments/assets/e8bfaf26-1c96-4edf-9df5-553bff722cf7" />

Oikea sivusto tuli esille.
## e) Porttiskannaa Metasploitable huolellisesti ja kaikki portit (nmap -A -T4 -p-). Poimi 2-3 hyökkääjälle kiinnostavinta porttia. Analysoi ja selitä tulokset näiden porttien osalta. Voit hakea analyysin tueksi tietoa verkosta, muista merkitä lähteet.

Porttiskannasin metasploitablen.

```
sudo nmap -A -T4 -p- 192.168.56.101
Starting Nmap 7.98 ( https://nmap.org ) at 2026-04-03 18:58 +0300
mass_dns: warning: Unable to determine any DNS servers. Reverse DNS is disabled. Try using --system-dns or specify valid servers with --dns-servers
Nmap scan report for 192.168.56.101
Host is up (0.00082s latency).
Not shown: 65505 closed tcp ports (reset)
PORT      STATE SERVICE     VERSION
21/tcp    open  ftp         vsftpd 2.3.4
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 192.168.56.1
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      vsFTPd 2.3.4 - secure, fast, stable
|_End of status
22/tcp    open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
| ssh-hostkey: 
|   1024 60:0f:cf:e1:c0:5f:6a:74:d6:90:24:fa:c4:d5:6c:cd (DSA)
|_  2048 56:56:24:0f:21:1d:de:a7:2b:ae:61:b1:24:3d:e8:f3 (RSA)
23/tcp    open  telnet      Linux telnetd
25/tcp    open  smtp        Postfix smtpd
|_smtp-commands: metasploitable.localdomain, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN
|_ssl-date: 2026-03-31T07:26:12+00:00; -3d08h34m46s from scanner time.
| sslv2: 
|   SSLv2 supported
|   ciphers: 
|     SSL2_RC4_128_WITH_MD5
|     SSL2_RC2_128_CBC_WITH_MD5
|     SSL2_DES_64_CBC_WITH_MD5
|     SSL2_RC4_128_EXPORT40_WITH_MD5
|     SSL2_DES_192_EDE3_CBC_WITH_MD5
|_    SSL2_RC2_128_CBC_EXPORT40_WITH_MD5
| ssl-cert: Subject: commonName=ubuntu804-base.localdomain/organizationName=OCOSA/stateOrProvinceName=There is no such thing outside US/countryName=XX
| Not valid before: 2010-03-17T14:07:45
|_Not valid after:  2010-04-16T14:07:45
53/tcp    open  domain      ISC BIND 9.4.2
| dns-nsid: 
|_  bind.version: 9.4.2
80/tcp    open  http        Apache httpd 2.2.8 ((Ubuntu) DAV/2)
|_http-server-header: Apache/2.2.8 (Ubuntu) DAV/2
|_http-title: Metasploitable2 - Linux
111/tcp   open  rpcbind     2 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2            111/tcp   rpcbind
|   100000  2            111/udp   rpcbind
|   100003  2,3,4       2049/tcp   nfs
|   100003  2,3,4       2049/udp   nfs
|   100005  1,2,3      35596/tcp   mountd
|   100005  1,2,3      38548/udp   mountd
|   100021  1,3,4      44397/tcp   nlockmgr
|   100021  1,3,4      54763/udp   nlockmgr
|   100024  1          40140/tcp   status
|_  100024  1          54786/udp   status
139/tcp   open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp   open  netbios-ssn Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)
512/tcp   open  exec        netkit-rsh rexecd
513/tcp   open  login       OpenBSD or Solaris rlogind
514/tcp   open  shell       Netkit rshd
1099/tcp  open  java-rmi    GNU Classpath grmiregistry
1524/tcp  open  bindshell   Metasploitable root shell
2049/tcp  open  nfs         2-4 (RPC #100003)
2121/tcp  open  ftp         ProFTPD 1.3.1
3306/tcp  open  mysql       MySQL 5.0.51a-3ubuntu5
| mysql-info: 
|   Protocol: 10
|   Version: 5.0.51a-3ubuntu5
|   Thread ID: 21
|   Capabilities flags: 43564
|   Some Capabilities: ConnectWithDatabase, LongColumnFlag, Support41Auth, Speaks41ProtocolNew, SwitchToSSLAfterHandshake, SupportsTransactions, SupportsCompression
|   Status: Autocommit
|_  Salt: stlBCjme2g4i6mH\xfxG
3632/tcp  open  distccd     distccd v1 ((GNU) 4.2.4 (Ubuntu 4.2.4-1ubuntu4))
5432/tcp  open  postgresql  PostgreSQL DB 8.3.0 - 8.3.7
|_ssl-date: 2026-03-31T07:26:12+00:00; -3d08h34m46s from scanner time.
| ssl-cert: Subject: commonName=ubuntu804-base.localdomain/organizationName=OCOSA/stateOrProvinceName=There is no such thing outside US/countryName=XX
| Not valid before: 2010-03-17T14:07:45
|_Not valid after:  2010-04-16T14:07:45
5900/tcp  open  vnc         VNC (protocol 3.3)
| vnc-info: 
|   Protocol version: 3.3
|   Security types: 
|_    VNC Authentication (2)
6000/tcp  open  X11         (access denied)
6667/tcp  open  irc         UnrealIRCd
6697/tcp  open  irc         UnrealIRCd
8009/tcp  open  ajp13       Apache Jserv (Protocol v1.3)
|_ajp-methods: Failed to get a valid response for the OPTION request
8180/tcp  open  http        Apache Tomcat/Coyote JSP engine 1.1
|_http-title: Apache Tomcat/5.5
|_http-server-header: Apache-Coyote/1.1
|_http-favicon: Apache Tomcat
8787/tcp  open  drb         Ruby DRb RMI (Ruby 1.8; path /usr/lib/ruby/1.8/drb)
35596/tcp open  mountd      1-3 (RPC #100005)
40140/tcp open  status      1 (RPC #100024)
44397/tcp open  nlockmgr    1-4 (RPC #100021)
51012/tcp open  java-rmi    GNU Classpath grmiregistry
MAC Address: 08:00:27:83:9F:6A (Oracle VirtualBox virtual NIC)
Device type: general purpose
Running: Linux 2.6.X
OS CPE: cpe:/o:linux:linux_kernel:2.6
OS details: Linux 2.6.9 - 2.6.33
Network Distance: 1 hop
Service Info: Hosts:  metasploitable.localdomain, irc.Metasploitable.LAN; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb-os-discovery: 
|   OS: Unix (Samba 3.0.20-Debian)
|   Computer name: metasploitable
|   NetBIOS computer name: 
|   Domain name: localdomain
|   FQDN: metasploitable.localdomain
|_  System time: 2026-03-31T03:26:02-04:00
|_clock-skew: mean: -3d07h34m46s, deviation: 2h00m00s, median: -3d08h34m46s
|_nbstat: NetBIOS name: METASPLOITABLE, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
|_smb2-time: Protocol negotiation failed (SMB2)

TRACEROUTE
HOP RTT     ADDRESS
1   0.82 ms 192.168.56.101

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 139.05 seconds
```

Hyökkääjälle kiinnostavimmat avoinna olevat portit olisivat todennäköisesti 1524, 23 ja 22.

Nmap tunnistaa portissa 1524 olevan root bind shell, eli portin ohjaa sisääntulevat yhteydet suoraan pääkäyttäjän shelliin. Tämä viittaa siihen, että koneeseen on murtautunut jo aiemmin joku.

Portti 21 on sen vuoksi kiinnostava, sillä sen takana pyörii vsftpd 2.3.4. Kyseinen versio on haavoittuvainen ja sisältää takaoven järjestelmään. (https://www.rapid7.com/db/modules/exploit/unix/ftp/vsftpd_234_backdoor/)

Portti 22 oli kolmas portti, jonka valitsin sillä kyseinen versio SSH:sta sisältää brute-force ja privilege escalation haavoittuvuudet. Näiden haavoittuvuuksien hyödyntämiseen löytyy payloadit suoraan metasploitista, joten lähes kuka tahansa pystyy niitä käyttäen saamaan root-oikeudet koneelle. (https://medium.com/@zendpushkar/ssh-exploitation-brute-force-attack-and-privilege-escalation-e0772c64a77d)

## f) Vapaaehtoinen bonus: Sisään vaan. Pääsetkö murtautumaan Metasploitableen?

Helpoin tapa murtautua metasploitableen on kävelemellä suoraan toisen hyökkääjän jättämästä takaovesta sisään. Yhdistin netcatilla suoraan portissa 1524 olevaan bind shelliin.

<img width="246" height="98" alt="image" src="https://github.com/user-attachments/assets/9c6b00ef-44a6-4b9a-9b1d-0afc9ee883c5" />
