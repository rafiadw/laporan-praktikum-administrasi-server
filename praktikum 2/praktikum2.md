## Soal Modul 2

1. Rubah LXC landing dengan ubuntu focal (destroy n create, same ip, same name)
2. Rubah LXC php7 dengan ubuntu focal (destroy n create, same ip, same name)
3. vm.local/
   - akan diinstall menggunakan framework laravel 8 pada lxc_landing
   - laravel 8 menggunakan php7.4
   - tentunya harus bisa connect ke server database (lxc_mariadb)
   - semua script instalasi tidak ada yang manual (kecuali openssh-server), harus menggunakan ansible, termasuk membuat database (sungguh mereka jumawa sekali)
4. vm.local/blog
   - install wordpress terbaru pada lxc_php7.4
   - wordpress menggunakan php7.4
   - tentunya harus bisa connect ke server database (lxc_mariadb)
   - semua script instalasi tidak ada yang manual (kecuali openssh-server), harus menggunakan ansible, termasuk membuat database (sungguh mereka jumawa sekali)
   - Bisa masuk dashboard

## Jawaban

1. Merubah versi ubuntu ubunt_landing dan ubuntu_php7.4 ke focal

   ```bash
   sudo su
   lxc-stop -n ubuntu_landing
   lxc-destroy ubuntu_landing
   lxc-stop -n ubuntu_php7.4
   lxc-destroy ubuntu_php7.4

   lxc-create -n ubuntu_landing -t download -- --dist ubuntu --release focal --arch amd64 --force-cache --no-validate --server images.linuxcontainers.org
   lxc-create -n ubuntu_landing -t download -- --dist ubuntu --release focal --arch amd64 --force-cache --no-validate --server images.linuxcontainers.org

   lxc-start -n ubuntu_landing
   lxc-start -n ubuntu_php7.4
   ```

2. Konfigurasi ip masing-masing container ke static dan konfigurasi ssh server

   - set ip static ubunutu_landing

   ```bash
   lxc-attach -n ubuntu_landing
   apt install nano net-tools curl
   nano /etc/netplan/10-lxc.yaml
   ```

   ![1]()

   ```bash
   netplan apply
   ```

   - set ssh serverr ubuntu_landing

   ```bash
   apt install openssh-server
   nano /etc/ssh/sshd_config

   # setting config menjadi
   PermitRootLogin yes
   RSAAuthentication yes

   # end of config
   service sshd restart

   ```

   - set password ssh server

   ```bash
   passwd
   ```

   - set ip static ubunutu_php7.4

   ```bash
   lxc-attach -n ubuntu_landing
   apt install nano net-tools curl
   nano /etc/netplan/10-lxc.yaml
   ```

   ![2]()

   ```bash
   netplan apply
   ```

   - set ssh serverr ubuntu_php7.4

   ```bash
   apt install openssh-server
   nano /etc/ssh/sshd_config

   # setting config menjadi
   PermitRootLogin yes
   RSAAuthentication yes

   # end of config
   service sshd restart

   ```

   - set password ssh server

   ```bash
   passwd
   ```

3. Installasi laravel pada container ubuntu_landing menggunaan ansible

   - buka directory ansible dan buat folder laravel

   ```bash
   cd ~/ansible/
   mkdir laravel
   cd laravel
   nano install-laravel.yml
   ```

   - isi seperti berikut

   ```bash
   - hosts: ubuntu_landing
   vars:
    username: 'admin'
    password: 'SysAdminSas0102'
    domain: 'lxc_landing.dev'
   roles:
    - php
    - lv
   ```

   - buat folder task dan handlers pada roles

   ```bash
   mkdir -p roles/php/tasks
   mkdir -p roles/php/handlers
   ```

   - buat file main.yml pada roles task

   ```bash
   nano roles/php/tasks/main.yml
   ```

   - isinya seperti berikut

   ```bash
   ---
    - name: delete apt chache
    become: yes
    become_user: root
    become_method: su
    command: rm -vf /var/lib/apt/lists/*

    - name: install php
    become: yes
    become_user: root
    become_method: su
    apt: name={{ item }} state=latest update_cache=true
    with_items:
        - curl
        - gtkhash
        - crack-md5
        - git
        - nginx
        - nginx-extras
        - php7.4
        - php7.4-fpm
        - php7.4-curl
        - php7.4-xml
        - php7.4-gd
        - php7.4-opcache
        - php7.4-mbstring

    - name: enable module php mbstring
    command: phpenmod mbstring
    notify:
        - restart php
   ```
