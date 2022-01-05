**modul praktikum 4**

```markdown
https://github.com/aldonesia/Sistem-Administrasi-Server-2021/blob/master/modul-4/silabus.md
```

**soal praktikum 4**

```markdown
1. Terapkan loadbalancer untuk /blog dan /app dengan ketentuan
   i./blog menggunakan least_conn
   ii./app menggunakan ip hash
   iii.disarakan menggunakan ansible untuk instalasi
2. Gunakan apache Jmeter untuk menganalisa perbedaan antara /, /app, /blog dengan loadbalancer dan tanpa loadbalancer pada traffic 50, 100 dan 150 users. Analisa dari segi waktu saja. Tulis langkah testing dan analisa dengan bahasa sendiri.
```

**laporan**

```markdown
1. Stop dulu debian_php5.6 dan ubuntu_php7.4, lalu clone
```

![1](https://user-images.githubusercontent.com/93067781/148239635-e85cb1c0-7a1f-4553-abc1-f4266ecfb871.png)

```markdown
2. Start semua termasuk clone debian_php5.6 dan ubuntu_php7.4
```

![2](https://user-images.githubusercontent.com/93067781/148239771-ef36c85b-f4dd-44d0-8f91-6986c13b1f02.png)

```markdown
3. Masuk ke ubuntu_landing_2
   nano /etc/hosts
```

![3](https://user-images.githubusercontent.com/93067781/148240545-5e25d260-3ffc-49f4-ba26-e15025eeb159.png)

```markdown
4. nano /etc/nginx/sites-available/lxc_landing.dev
```

![4](https://user-images.githubusercontent.com/93067781/148240491-bea75369-f1d8-40af-bcb8-11cd088ab9d0.png)

```markdown
5. Check konfigurasi
```

```markdown
    nginx -t
    service nginx restart
    curl -i http://lxc_landing_2.dev
```

![5](https://user-images.githubusercontent.com/93067781/148240612-817e0be3-5031-4c40-8d7f-ec641c733cc0.png)

```markdown
exit
```

```markdown
6. Pastikan semua lxc kondisi running
   lxc-ls-f
```

![6](https://user-images.githubusercontent.com/93067781/148240855-92642b13-eed3-4bfa-8a81-b94c10d82123.png)

```markdown
7. - Rename test plan menjadi Landing Load test
   - Tambahkan User Defined VariablesPermalink (klik kanan node Test Plan (Landing Load Test) -> Add -> Config Element -> User Defined Variables)
   - Tambahkan host dan port
   - Menambahkan Thread Group
   - Menambahkan HTTP Request Defaults
     Dengan cara klik kanan Landing Load Test-> add -> config element -> HTTP Request Default
   - Menambahkan HTTP Request untuk users access (Threads Group)
     Dengan cara klik kanan di user access-> add -> Sampler -> HTTP Request (lakukan 3 kali untuk landing, blog, dan app)
   - Menambahkan Graph Result
     Dengan cara klik kanan Landing Load Test -> Add > Listener > Graph Result
   - Menambahkan View Result in Table
     Dengan cara klik kanan Landing Load Test -> Add > Listener > View Result in Table
   - Menambahkan Summary Report
     Dengan cara klik kanan Landing Load Test -> Add > Listener > Summary Report
```

```markdown
lalu Run Testing Menjalankan Test secara otomatis. Caranya :

- Simpan terlebih dahulu Test Plan yang telah kita buat di File > Save ( Ctrl + S ).
- Klik Run atau Ctrl + R, jMeter akan mulai mensimulasi sejumlah user dalam mengakses web server yang telah ditentukan.
```

```markdown
Hasil Graph Result traffic 50 users Tanpa loadbalancer
```

![7](https://user-images.githubusercontent.com/93067781/148240862-0534c8f1-57b1-4c1a-b9e2-6c12ea4c82ec.png)

```markdown
8. Lihat Hasil View Result in Table 50 users Tanpa loadbalancer
```

![8](https://user-images.githubusercontent.com/93067781/148240871-c0e860c7-3bd8-4c27-ab08-00fd087987b1.png)

```markdown
9. Hasil Summary Report 50 users Tanpa loadbalancer
```

![9](https://user-images.githubusercontent.com/93067781/148240877-994e0cf5-f69e-43d1-a746-94b32f5443a0.png)

```markdown
10. Hasil Graph Result traffic 100 users Tanpa loadbalancer
```

![10](https://user-images.githubusercontent.com/93067781/148240889-fa816bbe-024c-4dd0-aa27-39d846911312.png)

```markdown
11. Lihat Hasil View Result in Table 100 users Tanpa loadbalancer
```

![11](https://user-images.githubusercontent.com/93067781/148241059-acb9cf12-2810-4946-826f-9b734e3b4a21.png)

```markdown
12. Hasil Summary Report 100 users Tanpa loadbalancer
```

![12](https://user-images.githubusercontent.com/93067781/148241066-54be8e16-15d5-4bcc-945f-77675a251826.png)

```markdown
13. Konfigurasi Load balancer
    sudo nano /etc/nginx/sites-available/vm.local
```

![13](https://user-images.githubusercontent.com/93067781/148241072-426c3f6f-211e-49d1-9488-b573c54804fd.png)

```markdown
Setelah selesai.
Check konfigurasi
sudo nginx -t
```

Menggunakan loadbalancer dapat mengoptimalkan pemanfaatan sumber daya, memaksimalkan throughput, mengurangi latensi, dan memastikan konfigurasi yang toleran terhadap kesalahan.

```markdown
14. Hasil Graph Result traffic 50 users Dengan loadbalancer
```

![14](https://user-images.githubusercontent.com/93067781/148241081-bf6bb560-5888-419d-b825-6128e72215a4.png)

```markdown
15. Lihat Hasil View Result in Table 50 users Dengan loadbalancer
```

![15](https://user-images.githubusercontent.com/93067781/148241088-6b50f1f2-f5fe-4df8-9b07-13b6574a1bf7.png)

```markdown
16. Hasil Summary Report 50 users Dengan loadbalancer
```

![16](https://user-images.githubusercontent.com/93067781/148241097-cff439dd-402f-4852-a1a4-374e5b88e41b.png)

```markdown
17. Hasil Graph Result traffic 100 users Dengan loadbalancer
```

![17](https://user-images.githubusercontent.com/93067781/148241102-6fb08c62-9d23-4a52-89df-b5c8f2ce10ba.png)

```markdown
18. Lihat Hasil View Result in Table 100 users Dengan loadbalancer
```

![18](https://user-images.githubusercontent.com/93067781/148241105-cb046269-65f4-4f5f-ab64-714e202229da.png)

```markdown
19. Hasil Summary Report 100 users Dengan loadbalancer
```

![19](https://user-images.githubusercontent.com/93067781/148241113-68125f6e-60de-4d42-ac2c-5fdf2f5e4b5a.png)

```markdown
20. Hasil Graph Result traffic 150 users Dengan loadbalancer
```

![20](https://user-images.githubusercontent.com/93067781/148241144-ca8a63b1-a949-4ad2-a607-75ab67adf846.png)

```markdown
21. Lihat Hasil View Result in Table 150 users Dengan loadbalancer
```

![21](https://user-images.githubusercontent.com/93067781/148241164-584d825f-4cc9-4e25-acec-d98a576dd00b.png)

```markdown
22. Hasil Summary Report 150 users Dengan loadbalancer
```

![22](https://user-images.githubusercontent.com/93067781/148241167-c431ca2a-9418-45d3-9420-848bf363286f.png)

23. Penjelasan Summary Report
    - Throughput : Menunjukkan jumlah request yang berhasil diproses per time unit (second, minute, hours) oleh server.
    - Received KB/sec : Mengindikasikan jumlah data yang berhasil diunduh oleh server selama dilakukannya eksekusi pengujian performance dalam satuan kilobyte tiap 1 sekon.
    - Sent KB/sec : Mengindikasikan jumlah data yang berhasil dikirim dari server selama dilakukannya eksekusi pengujian performance dalam satuan kilobyte tiap 1 sekon.
      Avg Bytes : Merupakan rata-rata byte yang berhasil diunduh (download) oleh server.
24. Analisis
    1. 50 user
    - tanpa load balacer
     Dalam waktu 0.2 (ramp-up) membutuhkan waktu rata eksekusi 1333 ms (landing), 5549 ms (blog), 580 ms (app) dengan waktu minimum yang dibutuhkan untuk     mengeksekusi thread 839 ms(landing), 34586 ms (blog), 3 ms (app) dan maksimumnya 1773 ms (landing), 60185 ms (blog), 5506 ms (app) dari througput server dapat mengeksekusi 28,2 thread/s (landing), 49.2 thread/min (blog), 1,9 thread/s
