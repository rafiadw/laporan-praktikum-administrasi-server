## Warning!!!

1. Sebelum melakukan langkah-langkah dibawah ini diharapkan kalian telah melakukan latihan pratikum  pada link berikut : https://github.com/aldonesia/Sistem-Administrasi-Server-2021/blob/master/modul-1/silabus.md
2. Link Soal Prakttikum Modul 1 : https://github.com/aldonesia/Sistem-Administrasi-Server-2021/blob/master/modul-1/soal_praktikum.md

## Laporan Tutorial Praktikum Modul 1

1. Langkah pertama yang kita lakukan adalah mengubah nama container sebelumnya yang telah kita buat pada latihan praktikum.  
```bash
sudo lxc-copy -R ubuntu_php5.6 -N ubuntu_landing
```
  - cek container apakah sudah berubah atau belum
  ```bash
  sudo lxc-ls -f
  ```
  ![Info Container](/praktikum%201/assets/p1.png)
  
2. Setelah selesai melakukan rename pada container ubuntu_php5.6, selanjutnya kita akan menginstall lxc debian 9 dengan nama debian_php5.6. 
```bash
sudo lxc-create -n debian_php5.6 -t download -- --dist debian --release stretch --arch amd64 --force-cache --no-validate --server images.linuxcontainers.org
sudo lxc-start -n debian_php5.6
```

3. Kemudian kita lakukan installasi nginx dan nginx-extras beserta konfigurasinya pada container debian_php5.6.

  - Installasi Nginx
```bash
sudo lxc-attach -n debian_php5.6
sudo apt install nginx nginx-extras
```

  - Konfigurasi static ip : 10.0.3.102
```bash
apt install nano net-tools curl
nano /etc/network/intefaces
```
![Configurasi IP](/praktikum%201/assets/p2.png)
```bash
systemctl restart networking.service
```
![Ifconfig](/praktikum%201/assets/p3.png)
  - Konfigurasi nginx
```bash
cd /etc/nginx/sites-available
touch lxc_php5.6.dev
nano lxc_php5.6.dev
```
![Info Container](/praktikum%201/assets/p4.png)
```bash
cd ..
cd sites-enabled
ln -s /etc/nginx/sites-available/lxc_php5.6.dev .
nginx -t
nginx -s reload
nano /etc/hosts
```
![Info Container](/praktikum%201/assets/p5.png)
```bash
cd /var/www/html
mkdir lxc_php5.6
cp index.nginx-debian.html lxc_php5/index.html
nano index.html
```
![Info Container](/praktikum%201/assets/p6.png)
```bash
curl -i http://lxc_php5.dev 
```
![Info Container](/praktikum%201/assets/p7.png)
```bash
exit
```
<<<<<<< HEAD
4. Karena pada latihan sebelumnya kita telah menginstall nginx dan nginx-extras maka selanjutnya kita tinggal melakukan konfigurasi ulang pada nginx dan ip addres container ubuntu_landing.
=======
4. Karena pada latihan sebelumnya kita telah menginstall nginx dan nginx-extras maka selanjutnya kita tinggal melakukan konfigurasi ulang pada nginx dan ip addres container ubuntu_landing
>>>>>>> 6614ae3638e3769a92cda85473dc01269956235f
```bash
sudo lxc-start -n ubuntu_landing
sudo lxc-attach -n ubuntu_landing
```

  - Konfigurasi static ip : 10.0.3.103
```bash
nano /etc/network/intefaces
```
![Iface](/praktikum%201/assets/p8.png)
```bash
systemctl restart networking.service
```
![Ifconfig](/praktikum%201/assets/p9.png)
  - Konfigurasi nginx
```bash
cd /etc/nginx/sites-available
touch lxc_landing.dev
nano lxc_landing.dev
```
![lxc_landing](/praktikum%201/assets/p10.png)
```bash
cd ..
cd sites-enabled
ln -s /etc/nginx/sites-available/lxc_landing.dev .
nginx -t
nginx -s reload
nano /etc/hosts
```
![host](/praktikum%201/assets/p11.png)
```bash
cd /var/www/html
mkdir lxc_landing
cp index.nginx-debian.html lxc_landing/index.html
nano index.html
```
![html](/praktikum%201/assets/p12.png)
```bash
curl -i http://lxc_landing.dev 
```
![curl](/praktikum%201/assets/p13.png)
```bash
exit
```
5. konfigurasi agar container autostart saat booting vm
   - Pastikan telah masuk super user
      ```bash
      sudo su
      ```
   - lalu lakukan penambahan lxc.auto.start = 1 pada masing-masing containers
      ```bash
      echo "lxc.start.auto = 1" >> /var/lib/lxc/ubuntu_landing/config
      echo "lxc.start.auto = 1" >> /var/lib/lxc/debian_php5.6/config
      echo "lxc.start.auto = 1" >> /var/lib/lxc/ubuntu_php7.4/config
      ```
   - Lakukan reboot dan cek containers apakah sudah berjalan tanpa melukan perintah lxc-start
     ![autostart](/praktikum%201/assets/p20.png)
6. Selanjutnya melakukan konfigurasi pada virtual machine hosts
  - konfigurasi host
```bash
sudo nano /etc/hosts
```
![hosts](/praktikum%201/assets/p14.png)
  - konfigurasi nginx (karena pada latihan sebelumnya kita telah menginstall nginx dan nginx-extras di vm maka kita tingal melakukan konfigurasi saja)
```bash
cd /etc/nginx/sites-available
sudo touch vm.local
sudo nano vm.local
```
![vm.local](/praktikum%201/assets/p19.png))
```bash
cd ..
cd sites-enabled
ln -s /etc/nginx/sites-available/vm.local .
sudo nginx -t
sudo nginx -s reload
curl -i http://vm.local
curl -i http://vm.local/blog
curl -i http://vm.local/app

```
7. Konfigurasi Hosts Laptop/Linux Mint
   - konfigurasi hosts
```bash
sudo nano /etc/hosts
```
![hosts linux](/praktikum%201/assets/p15.png)
  - cek browser
    - http://vm.local/
      ![/](/praktikum%201/assets/p18.png)
    - http://vm.local/app
      ![app](/praktikum%201/assets/p16.png)
    - htttp://vm.local/blog
      ![blog](/praktikum%201/assets/p17.png)
