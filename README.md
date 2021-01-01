# Menangani DDos pada Apache dengan Mod-Evasive
Mod-Evasive adalah module tambahan pada Apache2 untuk mencegah serangan DDoS pada level aplikasi Web.
```
apt-get install libapache2-mod-evasive
```
Selanjutnya adalah melakukan konfigurasi mod_evasive dengan membuat file:
```
pico /etc/apache2/conf.d/mod_evasive.conf
  DOSHashTableSize    2048
  DOSPageCount        5
  DOSSiteCount        100
  DOSPageInterval     1
  DOSSiteInterval     2
  DOSBlockingPeriod   10

  DOSEmailNotify      you@yourdomain.com
  DOSLogDir           "/var/log/mod_evasive"
```
Adapun penjelasan untuk masing-masing parameter adalah sebagai berikut:<br>
1. DOSHashTableSize adalah ukuran top level node hash table yang dibutuhkan oleh mod_evasive, jika terlalu kecil, maka kinerja web menjadi rendah, disarankan untuk menggunakan ukuran yang besar untuk web yang memiliki load yang besar.<br>
2. DOSPageCount adalah jumlah pemanggilan pada halaman yang sama untuk satu satuan waktu yang ditetapkan pada DOSPageInterval, jika angka ini dilampaui, maka akan mengembalikan 403 (Forbidden), dan IP di Blacklist selama DOSBlockingPeriod.<br>
3. DOSSitePageCount adalah jumlah pemanggilan pada website untuk satu satuan waktu yang ditetapkan pada DOSSiteInterval, jika angka ini dilampaui, maka akan mengembalikan 403 (Forbidden), dan IP di Blacklist  selama DOSBlockingPeriod.<br>
4. DOSPageInterval adalah satuan waktu (detik) untuk perhitungan DOSPageCount.<br>
5. DOSSiteInterval adalah satuan waktu (detik) untuk perhitungan DOSSiteCount.<br>
6. DOSBlockingPeriod adalah satuan waktu (detik) untuk blacklist IP.<br>
dan buatlah folder untuk menampung mod_evasive
```
mkdir /var/log/mod_evasive
chown www-data /var/log/mod_evasive
```
dan aktifkan module mod_evasive
```
a2enmod mod_evasive
service apache2 restart
```
jalan program test.pl yang disediakan untuk mengetest apakah mod_evasive telah berfungsi:
```
perl /usr/share/doc/libapache2-mod-evasive/examples/test.pl
```
Maka akan tampil HTTP/1.1 403 Forbidden<br>
Lakukan instalasi software Benchmark Apache untuk melakukan pengujian
```
apt-get install apache2-utils
```
dan kita akan mensimulasikan serangan Dos untuk melihat efektivitas dari setting Mod_Evasive
```
ab -n 100 -c 10 http://localhost/index.html
tail /var/log/syslog
tail /var/log/apache2/access.log
```
dan akan tampil bahwa ip 127.0.0.1 kena blacklist pada syslog, dan 403 pada access.log
