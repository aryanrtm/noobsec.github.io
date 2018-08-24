---
layout: post
title: Stored XSS di 1cak Posts
date: 2018-08-24 05:10:00 PM
permalink: project/2018-08-24-stored-xss-di-1cak-posts/
author: xhellc0de
categories: pentesting
tags: vulnerability xss stored-xss
---
![Image of Ad Networks](https://noobsec.org/images/1cak.jpg)

Setelah lelah dengan aktifitas sehari-hari, biasanya saya mencari hiburan di Internet. Selain [Xhamster](https://noobsec.org/project/2017-08-18-xhamster-ssrf-vulnerabillity/), saya juga suka mengunjungi situs 1cak untuk melihat karya-karya anak bangsa hehe.

Melihat banyaknya konten di situs tersebut, saya jadi terinspirasi untuk ikut memposting konten di 1cak. Namun namanya orang iseng, selalu ada selintas pertanyaan "Kira-kira di web ini ada bug nya nggak ya?". Untuk mengetahui hal tersebut tentu saja harus dicoba langsung.

Lalu saya mencoba membuat postingan gambar, dan memasukan karakter " pada field **Judul**, berharap ada bug XSS pada situs ini. Dan benar saja! setelah gambar berhasil di-post, ada tag HTML yang **rusak** pada halaman Post.

Berikut tag HTML nya:


><img onmouseover="return false" title="**ini judulnya"enaena**" src="https://cdn16.1cak.com/posts/xxx.jpg">

Terlihat bahwa Elemen title pada tag <img> tidak disanitasi dengan baik, sehingga kita dapat menyisipkan tag HTML didalamnya. Selanjutnya saya kembali memposting ulang gambar dengan mengisi field **Judul** dengan: 

>judulnya" onload="alert(document.cookie)"

![Image of 1cak XSS](https://noobsec.org/images/1cak_xss.jpg)

Dan payload XSS pun tereksekusi!

Saya segera melapor pada admin situs 1cak. Tidak lama kemudian dia membalas dan mengatakan bahwa bug tersebut telah selesai dipatch. Luar biasa cepat!

Dan saya pun diberi reward berupa pulsa atas temuan ini. Hehe lumayan..

![Image of 1cak Email](https://noobsec.org/images/1cak_email.jpg)

