---
layout: post
title: Traveloka Bug - Open Redirect
date: 2018-08-20 02:00:00 AM
permalink: 2018-08-20-traveloka-bug-open-redirect/
author: xhellc0de
categories: pentesting
tags: vulnerability open-redirect traveloka bug-bounty
---
![Image of Traveloka](https://noobsec.org/images/tv.png)

Berawal ketika selesai memesan Hotel di situs traveloka.com, saya pun membuka *email* untuk melihat Voucher Hotel yang baru saja saya pesan. Ketika membaca kata-kata mutiara pada *email* yang masuk, saya pun melihat ada tautan yang menuju ke *link* yang memuat file PDF dari Voucher saya tadi. 

Link tersebut bentuknya kurang lebih seperti ini:
```
https://www.traveloka.com/r?id=1607148893701&target=http://www.traveloka.com/pdf/xx.pdf
```

Pada *URL* tersebut terlihat ada 2 *parameter*, yakni **id** dan **target**.

Iseng-iseng saya merubah value dari parameter **id**, dan wow redirect tetap berjalan!

![Image of Happy](https://noobsec.org/images/happy.gif)

Dengan ini, asumsi saya kemungkinan website Traveloka memiliki bug *Open Redirect*. Sebelum dilanjutkan, saya membuka halaman Traveloka Bugs untuk mengecek apakah Open Redirect termasuk ke dalam scope mereka.

```
Open redirect menuju Google.com, Facebook.com, Twitter.com atau domain manapun yang kami anggap sebagai situs web terpercaya.
```

Oke, *Open Redirect* termasuk ke dalam scope mereka asalkan itu selain menuju ke website yang mereka anggap sebagai situs terpercaya. Tapi masalahnya kita tidak tau domain mana saja yang mereka anggap website terpercaya.

Selepas dari itu, mari kita coba untuk melakukan pengecekan lebih dalam. Pertama saya mencoba membuka URL berikut: 

```
https://www.traveloka.com/r?id=1&target=http://www.xvideos.com
```

Ketika dibuka halaman dialihkan ke traveloka.com. Artinya setiap parameter **target** berisi domain yang "tidak terpercaya" akan segera dialihkan ke index website Traveloka.

Saya mencoba mengganti-ganti format URL, namun hasilnya sia-sia. Ternyata memang URL parsernya tidak bisa di-bypass.

![Image of Sad](https://noobsec.org/images/sad.gif)

Setelah menghabiskan segelas kopi, saya terpikirkan sebuah cara yang mungkin bisa berhasil. Bagaimana kalau parameter **target** kita isi dengan *URL Shortener*!
Kurang lebih flow nya seperti ini:

```
Traveloka.com => URL Shortener => Website Tidak Terpercaya
```

Langung saya masukan link URL Shortener dari goo.gl karena saya pikir URL Shortener tersebut milik Google dan mungkin termasuk kedalam **Website yang terpercaya** oleh traveloka.

Lagi-lagi halaman dialihkan ke index Traveloka yang menyatakan bahwa domain goo.gl pun tidak termasuk kedalam **Website terpercaya**.

Lalu saya terpikirkan URL Shortener lain yang cukup familiar. URL Shortener ini sering dipakai oleh website-website perusahaan besar, yaitu Bitly.

Saya mencoba memasukan URL Bitly, dan berhasil! Halaman dialihkan ke **Website tidak terpercaya.**

Setelah ini saya segera melapor pada Traveloka terkait kemungkinan Bug yang saya temukan ini. Walaupun saya sedikit pesimis karena bisa saja itu terjadi karena Bitly termasuk **Website terpercaya.** Tapi yah namanya juga iseng, dicoba saja hehe.

Beberapa hari kemudian Traveloka membalas email dan menyatakan bug yang saya temukan adalah valid!

![Image of Bug Valid](https://noobsec.org/images/email_tv.jpg)

Gak nyangka juga berawal dari iseng ngutak-ngatik URL eh malah nemu Bug, hehe. Begitulah cerita saya menemukan Bug Open Redirect pada situs domain traveloka.com.

Saat ini status Bug sudah di Patch.

Referensi:
* [Unvalidated Redirects and Forwards Cheat Sheet](https://www.owasp.org/index.php/Unvalidated_Redirects_and_Forwards_Cheat_Sheet)
* [A New Era Of SSRF Exploiting URL Parser](https://www.blackhat.com/docs/us-17/thursday/us-17-Tsai-A-New-Era-Of-SSRF-Exploiting-URL-Parser-In-Trending-Programming-Languages.pdf)
