# ModulJarkomInformatikaITTS

# FIREWALL

**1. Definisi Firewall** 

Firewall adalah sistem yang digunakan untuk mengontrol akses jaringan dan melindungi sistem dari serangan atau aktivitas yang tidak diinginkan. Firewall dapat diimplementasikan pada perangkat keras atau perangkat lunak, dan dapat dikonfigurasi untuk memblokir atau mengizinkan akses berdasarkan aturan yang ditetapkan. Firewall dapat digunakan untuk melindungi jaringan internal dari serangan dari jaringan eksternal, atau untuk membatasi akses jaringan internal ke jaringan eksternal.


**2. Fungsi Firewall**

Firewall diperlukan karena keamanan, diantara pertimbangan adanya firewall adalah:
- Pencurian data pada jaringan internal
- Pengaksesan data oleh orang yang tidak berhak
- [Denial of Service]

**3. Cara Kerja Firewall**

![Ilustrasi](img/illustration.jpg)
Gambar 1. Ilustrasi Firewall (sumber: https://www.tunnelsup.com/images/firewall1.png)

Firewall bekerja dengan mengontrol akses jaringan berdasarkan aturan yang ditetapkan. Aturan tersebut dapat diterapkan pada paket data yang melewati jaringan, dan dapat dikonfigurasi untuk memblokir atau mengizinkan akses berdasarkan kriteria seperti alamat IP sumber, nomor port, protokol, dan konten data.

**4. Jenis - Jenis Firewall**
Beberapa jenis firewall yang umum digunakan adalah:

- Firewall Stateful Inspection: menyimpan informasi tentang koneksi jaringan yang saat ini aktif dan hanya mengizinkan paket data yang masuk atau keluar untuk koneksi yang sah.

- Firewall Application-Level Gateway: menyaring paket data berdasarkan protokol aplikasi tertentu, seperti HTTP atau FTP.

- Firewall Packet-Filtering: menyaring paket data berdasarkan kriteria seperti alamat IP sumber, nomor port, dan protokol.

- Firewall Next-Generation: merupakan firewall yang menyertakan kemampuan deep packet inspection, intrusion prevention system, dan sandwish firewall.



Pada Modul kali ini kita akan mempelajari bagaimana **Packet-Filtering Firewall** menggunakan command `$ iptables`. untuk dokumentasi dan cara penggunaannya bisa dilihat pada `$ man iptables`.

**5. Iptables**

![Struktur IPTables pada Komputer](img/netfilter-iptables-diagram-a.jpg)

Iptables adalah perangkat lunak firewall yang digunakan pada sistem operasi Linux. Iptables digunakan untuk mengontrol akses jaringan dengan menyaring paket data yang melewati jaringan dan menerapkan aturan yang ditetapkan. Iptables dapat digunakan untuk mengizinkan atau memblokir akses berdasarkan kriteria seperti alamat IP sumber, nomor port, protokol, dan konten data. Iptables dapat digunakan untuk melindungi jaringan internal dari serangan dari jaringan eksternal, atau untuk membatasi akses jaringan internal ke jaringan eksternal.

Struktur kerja IPTables,

    iptables -> Tables -> Chains -> Rules

dapat digambarkan dengan struktur seperti ini.

![IPTables Structure](img/iptables-table-chain-rule-structure.png)

**5.1. Table and Chains**

Macam-macam table pada iptables

**A. Filter Table**

Filter table pada iptables adalah salah satu dari empat tabel yang digunakan oleh iptables untuk mengontrol akses jaringan. Tablle filter digunakan untuk menyaring paket data yang melewati jaringan dan menerapkan aturan yang ditetapkan.
Pada tabel filter, iptables memiliki tiga chain yang digunakan untuk mengontrol akses jaringan, yaitu:

- **INPUT**: digunakan untuk mengontrol akses jaringan yang ditujukan ke host yang menjalankan iptables. Contoh Syntax :
  ```bash
  $ iptables --append INPUT --source 10.151.36.0/24 --jump DROP
  $ iptables -A INPUT -s 10.151.36.0/24 -j DROP
  ```
    Penjelasan:
    - DROP semua paket masuk (INPUT) yang berasal dari subnet 10.151.36.0/24

- **FORWARD**: digunakan untuk mengontrol akses jaringan yang dilewatkan oleh host yang menjalankan iptables. Contoh Syntax :
 ``` bash
  $ iptables --append FORWARD --source 10.151.36.0/24 --jump ACCEPT
  $ iptables -A FORWARD -s 10.151.36.0/24 -j ACCEPT
  ```  
  Penjelasan:
  - ACCEPT semua paket keluar yang melewati firewall yang berasal dari 10.151.36.0/24
  
- **OUTPUT**: digunakan untuk mengontrol akses jaringan yang dikeluarkan oleh host yang menjalankan iptables. Contoh Syntax :
 ```bash
  $ iptables --append OUTPUT --destination 10.151.36.5 --jump DROP
  $ iptables -A OUTPUT -d 10.151.36.5 -j DROP
  ```  
    Penjelasan:
    - DROP semua paket keluar (OUTPUT) yang menuju 10.151.36.5
![Filter Chain](img/iptables-tutorial-input-forward-output.jpg)
  
    
**B. Nat Table**
NAT table pada iptables adalah salah satu dari empat tabel yang digunakan oleh iptables untuk mengontrol akses jaringan. NAT table digunakan untuk melakukan Network Address Translation (NAT) pada paket data yang melewati jaringan. NAT digunakan untuk mengubah alamat IP sumber atau tujuan paket data sebelum paket data tersebut melewati jaringan.

Pada NAT table, iptables memiliki beberapa chain yang digunakan untuk melakukan NAT, yaitu:

- **PREROUTING**: digunakan untuk mengubah alamat IP sumber paket data sebelum paket data tersebut melewati routing table. Contoh Syntax :
  ```bash
  $ iptables --table nat --append PREROUTING --in-interface eth0 --protocol tcp --dport 80 --jump DNAT --to-destination 10.151.73.98:80

  $ iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 80 -j DNAT --to-destination 10.151.73.98:80
  ```
- **POSTROUTING**: digunakan untuk mengubah alamat IP tujuan paket data setelah paket data tersebut melewati routing table. Contoh Syntax :
```bash
   $ iptables --table nat --append POSTROUTING --out-interface eth0 --jump MASQUERADE
   $ iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
   ```
    Rule diatas berarti, source address dari setiap paket yang keluar (`-o`) melalui `eth0` akan diubah menjadi IP dari `eth0` (`MASQUERADE`).
    
**C. Mangle Table**

Mangle Table berfungsi untuk melakukan perubahan pada paket data. Perubahan yang dilakukan pada TCP header untuk memodifikasi QOS (*Quality of Service*) pada paket tersebut. Mangle Table memiliki *built-in chain*, yaitu :

- **PREROUTING** chain
- **OUTPUT** chain
- **FORWARD** chain
- **INPUT** chain
- **POSTROUTING** chain

3 Table utama pada IPTables dapat digambarkan sebagai berikut.

![Filter-NAT-Mangle Table](img/iptables-filter-nat-mangle-tables.png)

**5.2. Rules**

Hal-hal yang perlu diingat untuk memberlakukan *rules* pada IPTables, yaitu :

- **Rules** mempunyai kriteria dan target.
- **Jika** kriteria sudah **sesuai**, firewall akan mengeksekusi *rule* pada target atau suatu nilai (parameter) yang disebutkan pada target.
- **Jika** kriteria **tidak sesuai**, firewall akan mengeksekusi *rule* yang **selanjutnya**.

Tujuan paket yang dapat di definisikan pada target, beberapa **target yang sering digunakan** adalah :

1. **ACCEPT** – Firewall akan mengizinkan paket data.
2. **DROP** – Firewall akan menolak paket data.
3. **RETURN** – Firewall akan berhenti mengeksekusi rangkaian *rules* pada chain untuk paket data tersebut dan aturan pada *chain* tersebut dieksekusi ulang.
4. **MASQUERADE** – Target yang hanya berlaku pada **NAT Table**. Digunakan untuk mendefinisikan paket diarahkan ke subnet mana. Biasanya hanya digunakan untuk IP dinamis, jika menggunakan IP statis, maka gunakan **SNAT** target.
5. **REJECT** – Firewall akan menolak paket dan mengirimkan error message.
6. **REDIRECT** – Target yang hanya berlaku pada **NAT Table**. Firewall akan mengarahkan paket ke device (mesin) yang digunakannya menggunakan *Primary IP address* (alamat localhost) interface-nya.

Untuk macam-macam target lebih lengkap, dapat dilihat pada dokumentasi extension dari iptables
```bash
$ man iptables-extension
```

#### Syntax

Secara umum, untuk memodifikasi aturan yang berlaku pada IPTables dengan menjalankan

```bash
$ iptables [-t table] command chain rules
    jika tidak disebutkan tablenya maka defaultnya filter
``` 
Beberapa command yang sering digunakan pada iptables :

| Command and Syntax                                        | Description                                                                             | Example                                         |
| --------------------------------------------------------- |:--------------------------------------------------------------------------------------- |:----------------------------------------------- |
| `-A, --append chain rule-specification`                   | menambahkan rules pada chain  | `$ iptables -A INPUT -s 10.151.36.0/24 -j DROP` 
| `-C, --check chain rule-specification`                    | mengecek rule apa saja yang berlaku pada chain    | `iptables -C INPUT -s 10.151.36.0/24 -j DROP`
| `-D, --delete chain {rule-specification \ rulenum}`      | menghapus rules pada chain    | `$ iptables -D INPUT -s 10.151.36.0/24 -j DROP`
| `-I, --insert chain [rulenum] rule-specification`         | menyisipkan rules pada urutan tertentu    | `$ iptables -I OUTPUT 2 -s 10.151.36.0/24 -j DROP`
| `-R, --replace chain rulenum rule-specification`          | mengganti rules pada chain tertentu   | `$ iptables -R OUTPUT 2 -s 10.151.36.0/24 -j DROP`
| `-L, --list [chain]`                                      | melihat daftar rules yang berlaku berdasarkan chain   | `$ iptables -L INPUT`
| `-S, --list-rules [chain]`                                | melihat semua rules yang berlaku pada firewall   | `$ iptables -S INPUT`, `iptables -n -L -v --line-numbers`
| `-F, --flush [chain]`                                     | menghilangkan semua rules pada chain tertentu (semua chain jika chain tidak disebutkan) | `$ iptables -F INPUT`

**Penjelasan :**

- `rule-specification` = `[matches...] [target]`
- `match` = `-m matchname [per-match-options]`
- `target` = `-j targetname [per-target-options]`
- `[]` = syntax tersebut bersifat opsional

Beberapa parameter yang perlu diketahui :

| Parameter                         | Descripton                        |
| --------------------------------- | :-------------------------------- |
| `[!] -p, --protocol protocolename`    | mendefinisikan opsi port yang digunakan paket
| `[!] -s, --source address`            | mendefinisikan opsi alamat asal dari paket
| `[!] -d, --destination address`       | mendefinisikan opsi alamat tujuan dari paket
| `-m, --match matchname`                 | mendefinisikan kesesuaian rule untuk tujuannya ke mana
| `-j, --jump targetname`                 | mendefinisikan rule akan menggunakan taeget yang mana
| `[!] -i, --in-interface name`       | mendefinisikan opsi interface yang dilihat masuk paketnya
| `[!] -o, --out-interface name`      | mendefinisikan opsi interface yang dilihat keluar paketnya

**Penjelasan :**
- `[!]` = bisa dinegasikan

Untuk opsi command maupun parameter lebih lengkap dapat dilihat pada dokumentasi `iptables`, 

```bash
$ man iptables-extension
```
