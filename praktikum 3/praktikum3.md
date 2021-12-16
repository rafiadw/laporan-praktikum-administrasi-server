## modul praktikum 3

https://github.com/aldonesia/Sistem-Administrasi-Server-2021/blob/master/modul-3/silabus.md

## soal praktikum 3

1. buat subdomain dev.vm.local dengan menggunakan ansible dengan beberapa aturan:
   -- Menggunakan ansible
   -- menggunakan lxc yang sama dengan yang digunakan dengan vm.local
   -- folder code harus berbeda dengan yang digunakan vm.local, gunakan /var/www/html/dev/{nama_app}
2. Daftarkan subdomain vm.local ke DNS
3. responsi di minggu ke 11

## laporan

1.  hapus vm local di hosts linux

```bash
nano /etc/hosts
```

![]() 2. tambahkan konfigurasi www.vm.local pada host virtual vm.local
![]() 3. restart nginx

```bash
sudo service nginx restart
```

4. cek dan catat ip address pada virtualbox
   ```bash
   ip addr show enp0s3
   ```
   ![]()
   ip virtualbox adalah 192.168.43.10
5. instalasi bind9
   ```bash
   nano ~/ansible/prak2/roles/php/tasks/main.yml
   ```
   tambahkan bind9 dan dnsutils
   ![]()
6. tambahkan konfigurasi 'Copy conf.local, Copy vm.local, Copy 43.168.192, Copy resolv.conf, and Copy named.conf.options'
   ```bash
   cd ..
   cd ..
   nano lv/tasks/main.yml
   ```
   ![]()
7. lalu buat file 'conf.local, vm.local, 43.168.192, resolv.conf, and named.conf.options' di lv/templates
   ```bash
   cd ..
   nano cd roles/lv/templates/43.168.192.in-addr.arpa
   ```
   ![]()
   ```bash
   nano cd roles/lv/templates/named.conf.local
   ```
   ![]()
   ```bash
   nano cd roles/lv/templates/named.conf.options
   ```
   ![]()
   ```bash
   nano cd roles/lv/templates/resolv.conf
   ```
   ![]()
   ```bash
   nano cd roles/lv/templates/vm.local
   ```
   ![]()
8. tambahkan konfigurasi unutk merestart bind9 di cd roles/lv/handlers
   ```bash
   cd ..
   nano handlers/main.yml
   ```
   ![]()
9. jalankan ansible pada ~/ansble/prak2
   ```bash
   ansible-playbook -i hosts install-laravel.yml -k
   ```
   ![]()
10. tambahkan sub domain dev.vm.local di /etc/hosts
    ```bash
    nano /etc/hosts
    ```
    ![]()
11. tambahkan konfigurasi "dev IN CNAME vm.local"
    ```bash
    lxc-attach -n ubuntu_landing
    nano /var/www/html/dev/landing/vm.local
    ```
    lalu kita restart
    ```bash
    sudo /etc/init.d/named restart
    host -t CNAME dev.vm.local
    ```
12. Buka browser di komputer lokal ketikan www.vm.loval
    ![]()
    ![]()
