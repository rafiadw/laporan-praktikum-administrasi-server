1. Langkah pertama yang kita lakukan adalah mengubah nama container sebelumnya yang telah kita buat pada latihan praktikum.  
```bash
sudo lxc-copy -n ubuntu_php5.6 -R landing_page
```
2. Setelah selesai melakukan rename pada container ubuntu_php5.6, selanjutnya kita akan menginstall lxc debian 9 dengan nama debian_php5.6. 
```bash
sudo lxc-create -n debian_php5.6 -t download -- --dist debian --release stretch --arch amd64 --force-cache --no-validate --server images.linuxcontainers.org
sudo lxc-start -n debian_php5.6
```

3. Kemudian kita lakukan installasi nginx dan nginx-extras beserta konfigurasinya pada container debian_php5.6.

*Installasi Nginx
```bash
sudo lxc-attach -n debian_php5.6
sudo apt install nginx nginx-extras
```

*Konfigurasi static ip : 10.0.3.102
```bash
apt install nano net-tools curl
nano /etc/network/intefaces
```

```bash
systemctl restart networking.service
```

*Konfigurasi nginx
```bash
cd /etc/nginx/sites-available
touch lxc_php5.6.dev
nano lxc_php5.6.dev
```

```bash
cd ..
cd sites-enable
ln -s /etc/nginx/sites-available/lxc_php5.6.dev .
nginx -t
nginx -s reload
nano /etc/hosts
```

```bash
cd /var/www/html
mkdir lxc_php5.6
cp index.nginx-debian.html lxc_php5/index.html
nano index.html
```

```bash
curl -i http://lxc_php5.dev 
exit
```

4. Karena pada latihan sebelumny kita telah menginstall nginx dan nginx-extras maka selanjutnya kita tinggal melakukan konfigurasi ulang pada nginx dan ip addres.

*Konfigurasi static ip : 10.0.3.102
```bash
nano /etc/network/intefaces
```

```bash
systemctl restart networking.service
```

*Konfigurasi nginx
```bash
cd /etc/nginx/sites-available
touch lxc_landing.dev
nano lxc_landing.dev
```

```bash
cd ..
cd sites-enable
ln -s /etc/nginx/sites-available/lxc_landing.dev .
nginx -t
nginx -s reload
nano /etc/hosts
```

```bash
cd /var/www/html
mkdir lxc_landing
cp index.nginx-debian.html lxc_landing/index.html
nano index.html
```

```bash
curl -i http://lxc_landing.dev 
exit