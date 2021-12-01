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

3. Installasi php dan laravel pada container ubuntu_landing menggunaan ansible

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

   - buat folder task dan handlers pada roles untuk php

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

   - buat file main.yml pada roles handlers

   ```bash
   nano roles/php/handlers/main.yml
   ```

   - isinya seperti berikut

   ```bash
   ---
    - name: restart php
    become: yes
    become_user: root
    become_method: su
    action: service name=php7.4-fpm state=restarted
   ```

   - buat folder task dan handlers pada roles untuk laravel

   ```bash
   mkdir -p roles/lv/tasks
   mkdir -p roles/lv/templates
   mkdir -p roles/lv/handlers
   ```

   - buat file main.yml pada roles task

   ```bash
   nano roles/lv/tasks/main.yml
   ```

   - isinya seperti berikut

   ```bash
   ---
    - name: delete apt chache
    become: yes
    become_user: root
    become_method: su
    command: rm -vf /var/lib/apt/lists/*

    - name: install composer
    shell: curl -sS https://getcomposer.org/installer | php
    args:
        chdir: /usr/src/
        creates: /usr/local/bin/composer
        warn: false
    become: yes

    - name: making composer to global path
    copy:
        dest: /usr/local/bin/composer
        group: root
        mode: '775'
        owner: root
        src: /usr/src/composer.phar
        remote_src: yes
    become: yes

    - name: creating landing directory
      file:
        path: /var/www/html/landing
        state: absent

    - name: create laravel project
      shell: /usr/local/bin/composer create-project laravel/laravel /var/www/html/landing --prefer-dist --no-interaction

    - name: copying file .env.template
      template:
        src=templates/env.template
        dest=/var/www/html/landing/.env

    - name: composer
      shell: cd /var/www/html/landing; /usr/local/bin/composer install --no-interaction

    - name: key
    shell: /usr/bin/php7.4 /var/www/html/landing/artisan key:generate

    - name: chmod
    become: yes
    become_user: root
    become_method: su
    command: chmod 777 -R /var/www/html/landing/storage

    - name: copying lv.conf
    template:
        src=templates/lv.conf
        dest=/etc/nginx/sites-available/{{ domain }}
    vars:
        servername: '{{ domain }}'

    - name: symlink lv.conf to sited-enabled
      command: ln -sfn /etc/nginx/sites-available/{{ domain }} /etc/nginx/sites-enabled/{{ domain }}
      notify:
    - restart nginx

    - name: writing {{ domain }} to /etc/hosts
      lineinfile:
        dest: /etc/hosts
        regexp: '.*{{ domain }}'
        line: "127.0.0.1 {{ domain }}"
        state: present
   ```

   - buat file env.template pada roles templates

   ```bash
   nano roles/lv/templates/env.template
   ```

   - isinya seperti berikut

   ```bash
   server {
     listen 80;
     listen [::]:80;
     access_log /var/log/nginx/vhostlaravel-access.log;
     error_log /var/log/nginx/vhostlaravel-error.Log;
     root /var/www/html/landing/public;
     index index.php index.html index.html;
     server_name lxc_landing.dev;

     location / {
              try_files $uri $uri/ /index.php?$query_string;
     }
     location ~ \.php$ {
            try_files $uri =404;
            fastcgi_split_path_info ^C.+\.php)(/.+)$;
            fastcgi_pass unix:/run/php/php7.4-fpm.sock;
            fastcgi_index index.php;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            include fastcgi_params;
      }
    }
   ```

   - buat file main.yml pada roles handlers

   ```bash
   nano roles/lv/handlers/main.yml
   ```

   - isinya seperti berikut

   ```bahs
   ---
    - name: restart php
    become: yes
    become_user: root
    become_method: su
    action: service name=php7.4-fpm state=restarted

    - name: restart nginx
    become: yes
    become_user: root
    become_method: su
    action: service name=nginx state=restarted
   ```
