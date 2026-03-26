# Harjoitus 1: Kybertappoketju
Kurssi: https://terokarvinen.com/tunkeutumistestaus/ \
Tehtävänanto: https://terokarvinen.com/palvelinten-hallinta/#h1-viisikko

## x) Lue/katso/kuuntele ja tiivistä 
**Herrasmieshakkerit tai Darknet Diaries, yksi vapaavalintainen jakso jommasta kummasta**
* Jakso: Valokuvien vainukoira, vieraana Teemu M. Nieminen | 0x41
  * Jakson alussa käytiin läpi ajankohtaisia aiheita
    * Signalin perustaja Moxy Marlinspiken luoma yksityisyyteen perustuva tekoälymalli Confer
    * EU-INC, joka pyrkii yhtenäistämään yrittäjyyden Euroopassa
  * Vieraana GEOINTiin erikoistunut avointen lähteiden tutkija Teemu M. Nieminen kuvaamassa valokuvien paikantamisprosessia

**Hutchins et al 2011: Intelligence-Driven Computer Network Defense Informed by Analysis of Adversary Campaigns and Intrusion Kill Chains**
* Kill chain, eli tappoketju on alunperin sotilaallinen käsite, joka kuvaa hyökkäyksen vaiheita ja vaiheellisuutta
* Hutchins paperissaan esittää mallin tappoketjusta, joka on laadittu kuvaamaan tietoverkkojen hyökkäysten vaiheita: Kybertappoketju
* Kybertappoketjun vaiheet: Reconnaisance, Weaponization, Delivery, Exploitation, Installation, Command and Control, Actions on Objectives

**Santos et al: The Art of Hacking (Video Collection): 4.3 Surveying Essential Tools for Active Reconnaissance**

**KKO 2003:36**
* 19.11.1998 nuoren atk-harrastajan porttiskannaus osunut Osuuspankkikeskus-OPK osuuskunnan tietojärjestelmään
* Kohteena ollut yritys nostanut syytteen asiasta käräjäoikeudessa, josta se siirtynyt aina valitusten kautta korkeimpaan oikeuteen
* Korkein oikeus katsonut, että syytetty on oikeudettomasti yrittänyt tunkeutua tietojärjestelmään

## a) Asenna Kali virtuaalikoneeseen. (Jos asennuksessa ei ole mitään ongelmia tai olet asentanut jo aiemmin, tarkkaa raporttia tästä alakohdasta ei tarvita).

Minulta löytyi ylimääräinen kannettava, joka on vain tällä kurssilla käytössä, joten asensin Kalin suoraan tämän raudalle. Asennuskuvana toimi [kali-linux-2026.1-installer-amd64.iso](https://cdimage.kali.org/kali-2026.1/kali-linux-2026.1-installer-amd64.iso). Asennuksen kanssa ei ollut minkäänlaisia ongelmia.

## b) Irrota Kali-virtuaalikone verkosta. Todista testein, että kone ei saa yhteyttä Internetiin (esim. 'ping 8.8.8.8')



## c) Porttiskannaa 1000 tavallisinta tcp-porttia omasta koneestasi (nmap -T4 -A localhost). Selitä komennon paramterit. Analysoi ja selitä tulokset.
Ajoin annetun komennon pääkäyttäjänä, jotta porttiskannaus toimisi optimaalisesti
```
sudo nmap -T4 -A localhost
```
* `sudo nmap`: nmap-ohjelman ajaminen pääkäyttäjänä
* `-T4`: käytetään agressiivista ajoitusta, nopeuttaa skannauksen ajoa
* `-A`: käyttöjärjestelmän tunnistus, versioiden tunnistus, skriptien käyttö ja traceroute
* `localhost`: skannauksen kohdeosoite

## d) Asenna kaksi vapaavalintaista demonia ja skannaa uudelleen. Analysoi ja selitä erot.

## e) Ratkaise vapaavalintainen kone HackTheBoxista. Omalle tasolle sopiva, useimmille varmaan Starting Pointista. Valitse kone, jota et ole ratkaissut vielä. Ei tunnilla näytetty Meow. (Propellihatuille: jos teet vaikeampia ei-starting-point koneita, niin retired tai vastaava kone, josta saa julkaista writeupin).

Tein HackTheBoxin Cap-koneen, sillä se nopealla vilkaisulla näytti olevan suosituin retired kategoriasta. Koneen kuvauksessa kerrotaan HTTP-palvelimessa olevan IDOR-haavoittuvuus, joka mahdollistaa tunnusten löytämisen, joilla päästään linux koneen rootiksi. (https://app.hackthebox.com/machines/Cap)

Latasin ensiksi koneeseen liitetyn VPN konfiguraation, jonka jälkeen yhdistin VPN palvelimeen suoraan NetworkManagerin kautta.

*Task 1: How many TCP ports are open?*

Porttiskannasin kohdekoneen, `sudo nmap 10.129.17.241`

<img width="346" height="115" alt="image" src="https://github.com/user-attachments/assets/0179f788-eece-4a1b-b311-8499078b2c84" />

Nmap näytti, että avoimia portteja on 3, joka kelpasi HackTheBoxille oikeaksi vastaukseksi.

<img width="949" height="190" alt="image" src="https://github.com/user-attachments/assets/8c4804d9-057d-4b75-969b-0f98945fe7e2" />

*Task 2: After running a "Security Snapshot", the browser is redirected to a path of the format /[something]/[id], where [id] represents the id number of the scan. What is the [something]?*

Yritin yhdistää selaimella skannauksesta löytyneeseen HTTP-palvelimeen 10.129.17.241:80, mutta sivusto ei auennut, sillä se ei saanut ladattua fontteja, sekä javascript ohjelmia CDN-palvelimilta (VPN salli yhteydet vain kohdeverkkoon). Testasin, jos curl saisi yhteyden sivustoon.

```
$ curl http://10.129.17.241/
<!DOCTYPE html>
<html class="no-js" lang="en">

<head>
    <meta charset="utf-8">
    <meta http-equiv="x-ua-compatible" content="ie=edge">
    <title>Security Dashboard</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="shortcut icon" type="image/png" href="/static/images/icon/favicon.ico">
    <link rel="stylesheet" href="/static/css/bootstrap.min.css">
    <link rel="stylesheet" href="/static/css/font-awesome.min.css">
    <link rel="stylesheet" href="/static/css/themify-icons.css">
    <link rel="stylesheet" href="/static/css/metisMenu.css">
    <link rel="stylesheet" href="/static/css/owl.carousel.min.css">
    <link rel="stylesheet" href="/static/css/slicknav.min.css">
    <!-- amchart css -->
    <link rel="stylesheet" href="https://www.amcharts.com/lib/3/plugins/export/export.css" type="text/css" media="all" />
    <!-- others css -->
    <link rel="stylesheet" href="/static/css/typography.css">
    <link rel="stylesheet" href="/static/css/default-css.css">
    <link rel="stylesheet" href="/static/css/styles.css">
    <link rel="stylesheet" href="/static/css/responsive.css">
    <!-- modernizr css -->
    <script src="/static/js/vendor/modernizr-2.8.3.min.js"></script>
</head>

<body>
    <!--[if lt IE 8]>
            <p class="browserupgrade">You are using an <strong>outdated</strong> browser. Please <a href="http://browsehappy.com/">upgrade your browser</a> to improve your experience.</p>
        <![endif]-->
    <!-- preloader area start -->
    <div id="preloader">
        <div class="loader"></div>
    </div>
    <!-- preloader area end -->
    <!-- page container area start -->
    <div class="page-container">
        <!-- sidebar menu area start -->
        <div class="sidebar-menu">
            <div class="sidebar-header">
            </div>
            <div class="main-menu">
                <div class="menu-inner">
                    <nav>
                        <ul class="metismenu" id="menu">
                            <li class="active">
                                <a href="javascript:void(0)" aria-expanded="true"><i class="ti-dashboard"></i><span>dashboard</span></a>
                                <ul class="collapse">
                                    <li class="active"><a href="/">Dashboard</a></li>
                                    <li><a href="/capture">Security Snapshot (5 Second PCAP + Analysis)</a></li>
                                    <li><a href="/ip">IP Config</a></li>
                                    <li><a href="/netstat">Network Status</a></li>
                                </ul>
                            </li>
                        </ul>
                    </nav>
                </div>
            </div>
        </div>
        <!-- sidebar menu area end -->
        <!-- main content area start -->
        <div class="main-content">
            <!-- header area start -->
            <div class="header-area">
                <div class="row align-items-center">
                    <!-- nav and search button -->
                    <div class="col-md-6 col-sm-8 clearfix">
                        <div class="nav-btn pull-left">
                            <span></span>
                            <span></span>
                            <span></span>
                        </div>
                        <div class="search-box pull-left">
                            <form action="#">
                                <input type="text" name="search" placeholder="Search..." required>
                                <i class="ti-search"></i>
                            </form>
                        </div>
                    </div>
                    <!-- profile info & task notification -->
                    <div class="col-md-6 col-sm-4 clearfix">
                        <ul class="notification-area pull-right">
                            <li id="full-view"><i class="ti-fullscreen"></i></li>
                            <li id="full-view-exit"><i class="ti-zoom-out"></i></li>
                        </ul>
                    </div>
                </div>
            </div>
            <!-- header area end -->
            <!-- page title area start -->
            <div class="page-title-area">
                <div class="row align-items-center">
                    <div class="col-sm-6">
                        <div class="breadcrumbs-area clearfix">
                            <h4 class="page-title pull-left">Dashboard</h4>
                            <ul class="breadcrumbs pull-left">
                                <li><a href="/">Home</a></li>
                                <li><span>Dashboard</span></li>
                            </ul>
                        </div>
                    </div>
                    <div class="col-sm-6 clearfix">
                        <div class="user-profile pull-right">
                            <img class="avatar user-thumb" src="/static/images/author/avatar.png" alt="avatar">
                            <h4 class="user-name dropdown-toggle" data-toggle="dropdown">Nathan<i class="fa fa-angle-down"></i></h4>
                            <div class="dropdown-menu">
                                <a class="dropdown-item" href="#">Message</a>
                                <a class="dropdown-item" href="#">Settings</a>
                                <a class="dropdown-item" href="#">Log Out</a>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            <!-- page title area end -->
            <div class="main-content-inner">


                <!-- sales report area start -->
                <div class="sales-report-area mt-5 mb-5">
                    <div class="row">
                        <div class="col-md-4">
                            <div class="single-report mb-xs-30">
                                <div class="s-report-inner pr--20 pt--30 mb-3">
                                    <div class="s-report-title d-flex justify-content-between">
                                        <h4 class="header-title mb-0">Security Events</h4>
                                        <p>24 H</p>
                                    </div>
                                    <div class="d-flex justify-content-between pb-2">
                                        <h2>1,560</h2>
                                        <span>+15%</span>
                                    </div>
                                </div>
                                <canvas id="coin_sales1" height="100"></canvas>
                            </div>
                        </div>
                        <div class="col-md-4">
                            <div class="single-report mb-xs-30">
                                <div class="s-report-inner pr--20 pt--30 mb-3">
                                    <div class="s-report-title d-flex justify-content-between">
                                        <h4 class="header-title mb-0">Failed Login Attempts</h4>
                                        <p>24 H</p>
                                    </div>
                                    <div class="d-flex justify-content-between pb-2">
                                        <h2>357</h2>
                                        <span>-10%</span>
                                    </div>
                                </div>
                                <canvas id="coin_sales2" height="100"></canvas>
                            </div>
                        </div>
                        <div class="col-md-4">
                            <div class="single-report">
                                <div class="s-report-inner pr--20 pt--30 mb-3">
                                    <div class="s-report-title d-flex justify-content-between">
                                        <h4 class="header-title mb-0">Port Scans (Unique IPs)</h4>
                                        <p>24 H</p>
                                    </div>
                                    <div class="d-flex justify-content-between pb-2">
                                        <h2>27</h2>
                                        <span>+28%</span>
                                    </div>
                                </div>
                                <canvas id="coin_sales3" height="100"></canvas>
                            </div>
                        </div>
                    </div>
                </div>
                <!-- sales report area end -->

            </div>
        </div>
        <!-- main content area end -->
        <!-- footer area start-->
        <footer>
            <div class="footer-area">
                <p>© Copyright 2021. All right reserved. Template by <a href="https://colorlib.com/wp/">Colorlib</a>.</p>
            </div>
        </footer>
        <!-- footer area end-->
    </div>
    <!-- page container area end -->
    <!-- offset area start -->
    <div class="offset-area">
        <div class="offset-close"><i class="ti-close"></i></div>
        <ul class="nav offset-menu-tab">
            <li><a class="active" data-toggle="tab" href="#activity">Activity</a></li>
            <li><a data-toggle="tab" href="#settings">Settings</a></li>
        </ul>
            <div id="activity" class="tab-pane fade in show active">
                <div class="recent-activity">
                    <div class="timeline-task">
                        <div class="icon bg1">
                            <i class="fa fa-envelope"></i>
                        </div>
                        <div class="tm-title">
                            <h4>Rashed sent you an email</h4>
                            <span class="time"><i class="ti-time"></i>09:35</span>
                        </div>
                        <p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Esse distinctio itaque at.
                        </p>
                    </div>
                    <div class="timeline-task">
                        <div class="icon bg2">
                            <i class="fa fa-check"></i>
                        </div>
                        <div class="tm-title">
                            <h4>Added</h4>
                            <span class="time"><i class="ti-time"></i>7 Minutes Ago</span>
                        </div>
                        <p>Lorem ipsum dolor sit amet consectetur.
                        </p>
                    </div>
                    <div class="timeline-task">
                        <div class="icon bg2">
                            <i class="fa fa-exclamation-triangle"></i>
                        </div>
                        <div class="tm-title">
                            <h4>You missed you Password!</h4>
                            <span class="time"><i class="ti-time"></i>09:20 Am</span>
                        </div>
                    </div>
                    <div class="timeline-task">
                        <div class="icon bg3">
                            <i class="fa fa-bomb"></i>
                        </div>
                        <div class="tm-title">
                            <h4>Member waiting for you Attention</h4>
                            <span class="time"><i class="ti-time"></i>09:35</span>
                        </div>
                        <p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Esse distinctio itaque at.
                        </p>
                    </div>
                    <div class="timeline-task">
                        <div class="icon bg3">
                            <i class="ti-signal"></i>
                        </div>
                        <div class="tm-title">
                            <h4>You Added Kaji Patha few minutes ago</h4>
                            <span class="time"><i class="ti-time"></i>01 minutes ago</span>
                        </div>
                        <p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Esse distinctio itaque at.
                        </p>
                    </div>
                    <div class="timeline-task">
                        <div class="icon bg1">
                            <i class="fa fa-envelope"></i>
                        </div>
                        <div class="tm-title">
                            <h4>Ratul Hamba sent you an email</h4>
                            <span class="time"><i class="ti-time"></i>09:35</span>
                        </div>
                        <p>Hello sir , where are you, i am egerly waiting for you.
                        </p>
                    </div>
                    <div class="timeline-task">
                        <div class="icon bg2">
                            <i class="fa fa-exclamation-triangle"></i>
                        </div>
                        <div class="tm-title">
                            <h4>Rashed sent you an email</h4>
                            <span class="time"><i class="ti-time"></i>09:35</span>
                        </div>
                        <p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Esse distinctio itaque at.
                        </p>
                    </div>
                    <div class="timeline-task">
                        <div class="icon bg2">
                            <i class="fa fa-exclamation-triangle"></i>
                        </div>
                        <div class="tm-title">
                            <h4>Rashed sent you an email</h4>
                            <span class="time"><i class="ti-time"></i>09:35</span>
                        </div>
                    </div>
                    <div class="timeline-task">
                        <div class="icon bg3">
                            <i class="fa fa-bomb"></i>
                        </div>
                        <div class="tm-title">
                            <h4>Rashed sent you an email</h4>
                            <span class="time"><i class="ti-time"></i>09:35</span>
                        </div>
                        <p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Esse distinctio itaque at.
                        </p>
                    </div>
                    <div class="timeline-task">
                        <div class="icon bg3">
                            <i class="ti-signal"></i>
                        </div>
                        <div class="tm-title">
                            <h4>Rashed sent you an email</h4>
                            <span class="time"><i class="ti-time"></i>09:35</span>
                        </div>
                        <p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Esse distinctio itaque at.
                        </p>
                    </div>
                </div>
            </div>
            <div id="settings" class="tab-pane fade">
                <div class="offset-settings">
                    <h4>General Settings</h4>
                    <div class="settings-list">
                        <div class="s-settings">
                            <div class="s-sw-title">
                                <h5>Notifications</h5>
                                <div class="s-swtich">
                                    <input type="checkbox" id="switch1" />
                                    <label for="switch1">Toggle</label>
                                </div>
                            </div>
                            <p>Keep it 'On' When you want to get all the notification.</p>
                        </div>
                        <div class="s-settings">
                            <div class="s-sw-title">
                                <h5>Show recent activity</h5>
                                <div class="s-swtich">
                                    <input type="checkbox" id="switch2" />
                                    <label for="switch2">Toggle</label>
                                </div>
                            </div>
                            <p>The for attribute is necessary to bind our custom checkbox with the input.</p>
                        </div>
                        <div class="s-settings">
                            <div class="s-sw-title">
                                <h5>Show your emails</h5>
                                <div class="s-swtich">
                                    <input type="checkbox" id="switch3" />
                                    <label for="switch3">Toggle</label>
                                </div>
                            </div>
                            <p>Show email so that easily find you.</p>
                        </div>
                        <div class="s-settings">
                            <div class="s-sw-title">
                                <h5>Show Task statistics</h5>
                                <div class="s-swtich">
                                    <input type="checkbox" id="switch4" />
                                    <label for="switch4">Toggle</label>
                                </div>
                            </div>
                            <p>The for attribute is necessary to bind our custom checkbox with the input.</p>
                        </div>
                        <div class="s-settings">
                            <div class="s-sw-title">
                                <h5>Notifications</h5>
                                <div class="s-swtich">
                                    <input type="checkbox" id="switch5" />
                                    <label for="switch5">Toggle</label>
                                </div>
                            </div>
                            <p>Use checkboxes when looking for yes or no answers.</p>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
    <!-- offset area end -->
    <!-- jquery latest version -->
    <script src="/static/js/vendor/jquery-2.2.4.min.js"></script>
    <!-- bootstrap 4 js -->
    <script src="/static/js/popper.min.js"></script>
    <script src="/static/js/bootstrap.min.js"></script>
    <script src="/static/js/owl.carousel.min.js"></script>
    <script src="/static/js/metisMenu.min.js"></script>
    <script src="/static/js/jquery.slimscroll.min.js"></script>
    <script src="/static/js/jquery.slicknav.min.js"></script>

    <!-- start chart js -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/2.7.2/Chart.min.js"></script>
    <!-- start highcharts js -->
    <script src="https://code.highcharts.com/highcharts.js"></script>
    <!-- start zingchart js -->
    <script src="https://cdn.zingchart.com/zingchart.min.js"></script>
    <script>
    zingchart.MODULESDIR = "https://cdn.zingchart.com/modules/";
    ZC.LICENSE = ["569d52cefae586f634c54f86dc99e6a9", "ee6b7db5b51705a13dc2339db3edaf6d"];
    </script>
    <!-- all line chart activation -->
    <script src="/static/js/line-chart.js"></script>
    <!-- all pie chart -->
    <script src="/static/js/pie-chart.js"></script>
    <!-- others plugins -->
    <script src="/static/js/plugins.js"></script>
    <script src="/static/js/scripts.js"></script>
</body>

</html>   
```
Sivusto latautui. Seurasin "Secure Snapshot"-linkkiä saadakseni kysymyksessä kysytyn polun.
```
$ curl http://10.129.17.241/capture
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 3.2 Final//EN">
<title>Redirecting...</title>
<h1>Redirecting...</h1>
<p>You should be redirected automatically to target URL: <a href="/data/1">/data/1</a>. If not click the link.   
```
<img width="944" height="203" alt="image" src="https://github.com/user-attachments/assets/6b63449b-3f43-4723-b9b4-07d1bf94c38e" />

*Task 3: Are you able to get to other user's scans?*

Otin tässä kohtaa openvpn:n käyttöön, jotta sivuston saa myös selaimella auki.

`sudo openvpn machines_us-1.ovpn`

Sivusto avautui.

<img width="1853" height="557" alt="image" src="https://github.com/user-attachments/assets/b781a794-31f4-4db7-a8a3-7d7edbd7f48e" />

Koneen infossa mainittiin sivulla olevan Insecure direct object reference -haavoittuvuus, eli pääsynhallintaan liittyvä haavoittuvuus, jota tyypillisesti hyödynnetään URL-osoitteen ja siihen liitettyjen parametrien kautta. (https://portswigger.net/web-security/access-control/idor)

Sivustolla Security Snapshotin luominen vei osoitteeseen `/data/1`, jossa snapshotin id löytyy polun päätteestä. Kokeilin muutamia erilaisia id:tä, joista 0 löytyi. 

<img width="1844" height="786" alt="image" src="https://github.com/user-attachments/assets/a9d63dce-0ddd-4866-8841-6bf47ceaf5e6" />

En ollut itse luonut kyseistä snapshotia, joten valitsin vastaukseksi "yes".

<img width="1370" height="189" alt="image" src="https://github.com/user-attachments/assets/b3bcd6f3-9214-4a47-bab6-e4b1fbb3cc56" />

*Task 4: What is the ID of the PCAP file that contains sensitive data?*

Latasin snapshotin 0 pcap-tiedoston sivun download-napista ja avasin sen Wiresharkilla.

Nopealla silmäyksellä huomasin, että se sisältää käyttäjän nathan FTP-salasanan.

<img width="1259" height="864" alt="image" src="https://github.com/user-attachments/assets/5ba12a17-13fc-4f50-8837-b3a9b9b74fe1" />

Kyseinen tiedosto siis sisältää herkkää dataa.

<img width="1360" height="193" alt="image" src="https://github.com/user-attachments/assets/9a3fb8c5-d5e1-470e-a90b-93cb39be7972" />

*Task 5: Which application layer protocol in the pcap file can the sensetive data be found in?*

Tiedostosta nähtiin, että salasana sijaitsee FTP-protokollassa.

<img width="1361" height="188" alt="image" src="https://github.com/user-attachments/assets/f99b80ac-b384-43f2-a4d4-5d7b48022d48" />

Lähdin kokeilemaan nathanin salasanaa Buck3tH4TF0RM3! ftp-palveluun ja pääsin sillä sisään.

<img width="292" height="116" alt="image" src="https://github.com/user-attachments/assets/97288b72-447d-4d8d-ae16-4325929ed7c9" />

Nathanin kotihakemistosta löytyi tiedosto user.txt ja latasin sen.

<img width="1249" height="193" alt="image" src="https://github.com/user-attachments/assets/e96a52a6-5169-4204-b256-812ae924205c" />

user.txt-tiedostosta löytyi toinen koneen kahdesta lipusta.

<img width="279" height="64" alt="image" src="https://github.com/user-attachments/assets/e10a1554-e5f9-4905-ba7b-6f89686baf0e" />

<img width="1367" height="176" alt="image" src="https://github.com/user-attachments/assets/4c87e917-98d9-47a5-9482-8111f14fe019" />

*Task 6: We've managed to collect nathan's FTP password. On what other service does this password work?*

Porttiskannauksen tuloksena tiedetään, että HTTP ja FTP palveluiden lisäksi koneella on SSH. Tutkin pääseekö samalla salanalla sisään SSH:n kautta.

<img width="621" height="649" alt="image" src="https://github.com/user-attachments/assets/7033e6ed-6e47-4fe8-be36-23cc45f6040f" />

Salasana Buck3tH4TF0RM3! toimi myös SSH:n.

<img width="1365" height="194" alt="image" src="https://github.com/user-attachments/assets/6a0c85fe-d7df-4cc6-a545-b9cc0e6e9945" />

*Task 8: What is the full path to the binary on this machine has special capabilities that can be abused to obtain root privileges?*

Käyttäjällä ei ole sudo oikeuksia ja rootilla on eri salasana.

<img width="558" height="99" alt="image" src="https://github.com/user-attachments/assets/4a56f668-57ed-4c91-a858-bd5ce087f206" />

Tässä kohtaa lähdin Googlailemaan järjestelmän haavoittuvuuksia. SSH:n bannerista löytyi kernelin malli ja haku "GNU/Linux 5.4.0-80-generic cve" tuotti tuloksia. Haku sisälsi Cap koneen walkthrough ratkaisuja, joten oletin myös tämän perusteella olevani oikeilla jäljillä.

<img width="1070" height="1163" alt="image" src="https://github.com/user-attachments/assets/79fe9c45-cc0f-49d0-80be-242af078dd25" />

Kyseiselle Ubuntun kernelille näyttäisi löytyvän useampia privilege escalation -haavoittuvuuksia, joista dokumentoiduin oli [CVE-2023-2640](https://ubuntu.com/security/CVE-2023-2640). Täman haavoittuvuuden testaukseen löytyi redditistä komento. (https://www.reddit.com/r/selfhosted/comments/15ecpck/ubuntu_local_privilege_escalation_cve20232640/).

Ajoin komennon ja se antoi pythonin ajaa pääkäyttäjänä.

<img width="1255" height="98" alt="image" src="https://github.com/user-attachments/assets/915924b1-45e4-48da-bcf6-ae4d6c01df54" />

Yritin tulostaa pythonia käyttäen root-käyttäjän kotihakemiston, mutta sain virheen puutteellisista oikeuksista.

<img width="731" height="287" alt="image" src="https://github.com/user-attachments/assets/84d812df-474c-4926-a524-c7cac211a3e2" />

Huomasin, että python ei automattiisesti aja pääkäyttäjänä ja vaihdettuani uid:n rootiksi sain tulostettua viimeisen lipun.

<img width="835" height="227" alt="image" src="https://github.com/user-attachments/assets/596c8531-5350-4372-ac78-6c0650a9e4ce" />

<img width="1377" height="583" alt="image" src="https://github.com/user-attachments/assets/0ba1f946-8425-419b-9d75-c88012bcd09e" />


## Lähteet

https://nmap.org/book/man-performance.html

https://portswigger.net/web-security/access-control/idor
