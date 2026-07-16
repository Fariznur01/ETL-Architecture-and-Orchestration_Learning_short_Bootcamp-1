 ETL-Architecture-and-Orchestration_Learning_short_Bootcamp-1

<br>

<div align="center">
  <img src="https://www.python.org/static/community_logos/python-logo-master-v3-TM.png"
       width="800"
       height="200"
       alt="Python Logo">
</div>

[![Python 3.14.3](https://img.shields.io/badge/Python-3.14.3-blue.svg)]()
![Visual Studio Code](https://img.shields.io/badge/Visual%20Studio%20Code-1.129-blue?logo=visualstudiocode)

<details>
<summary>Table of Contents</summary>

- <a href="#overview">Overview</a>
- <a href="#extract">Extract</a>
- <a href="#transform">Transform</a>
- <a href="#load">Load</a>
- <a href="#orchestration">Orchestration</a>
- <a href="#error-handling">Error Handling</a>
- <a href="#monitoring">Monitoring</a>

</details>

## Overview


# ETL Pipeline Design: E-Commerce Orders 

Proyek ini membangun ETL/ELT Data Pipeline untuk memproses data transaksi e-commerce harian. Pipeline dirancang untuk mengotomatisasi proses pengambilan data, pembersihan, validasi, transformasi, hingga penyimpanan ke dalam bentuk file data bersih sehingga data siap digunakan untuk analisis, dashboard, maupun kebutuhan business intelligence.  

Pipeline dirancang dengan prinsip automation, reproducibility, scalability, dan data quality, sehingga mampu memproses data harian secara konsisten serta mempermudah pengembangan pipeline di masa mendatang. 

## Extract

- Sumber:  Data Dummy short  bootcamp data engineer dibimbing dengan file raw_orders.csv dan raw_products.csv

- Format: Data kotor dengan formar csv
- Volume: Jumlah data 130 raw_orders dan jumlah data 10 raw_product


## Transform
- Langkah 1: 
Langkah  pertama dalam pipeline ini adalah Extract, yaitu proses mengambil data transaksi e-commerce harian dari sumber data berupa file CSV (raw_orders.csv) ke dalam lingkungan pemrosesan Python menggunakan library Pandas.
Selain membaca data, pada tahap ini juga dilakukan data profiling dan data quality checking untuk memahami struktur dataset serta mengidentifikasi potensi masalah sejak awal.


Tahap Extract tidak hanya bertujuan mengambil data, tetapi juga memastikan bahwa data yang masuk ke pipeline memiliki kualitas yang baik. Jika masalah seperti duplikasi, missing values, atau format data yang salah tidak terdeteksi sejak awal, maka proses transformasi dan analisis selanjutnya dapat menghasilkan informasi yang tidak akurat.

Aktivitas yang Dilakukan:
*Mengimpor dataset transaksi (orders).
*Memeriksa jumlah baris dan kolom pada dataset.
*Mengidentifikasi tipe data setiap kolom.
*Mendeteksi data duplikat.
*Memeriksa missing values pada setiap kolom.
*Memvalidasi nilai yang tidak wajar, seperti total_harga bernilai negatif.
*Melihat nilai unik pada kolom kategorikal seperti channel dan kota untuk menemukan kemungkinan inkonsistensi penulisan.


- Langkah 2
Tahap Transform bertujuan untuk membersihkan, memvalidasi, dan menstandarkan data hasil ekstraksi sehingga memiliki kualitas yang baik dan siap digunakan pada proses penyimpanan (Load). Pada tahap ini dilakukan berbagai proses data cleaning dan enrichment agar data menjadi konsisten, akurat, serta mudah dianalisis. 

Data mentah (raw data) sering kali mengandung berbagai permasalahan, seperti data duplikat, nilai yang tidak valid, missing values, format data yang tidak konsisten, hingga perbedaan penulisan pada data kategorikal. Jika tidak diperbaiki, masalah tersebut dapat menyebabkan kesalahan dalam analisis maupun proses pemuatan ke database. Oleh karena itu, tahap transformasi menjadi langkah penting untuk meningkatkan kualitas dan keandalan data. 

 Aktivitas yang Dilakukan: 
*Menghapus data duplikat untuk memastikan setiap transaksi hanya tercatat satu kali.
*Menghapus data dengan nilai transaksi negatif karena dianggap sebagai data yang tidak valid atau hasil kesalahan input.
*Menangani missing values, yaitu:
*Mengisi email pelanggan yang kosong dengan nilai placeholder (unknown@placeholder.com).
*Mengisi nilai total_harga yang kosong menggunakan nilai median agar distribusi data tidak terlalu terpengaruh oleh outlier.
*Menstandarkan tipe data dengan mengubah kolom tanggal_order menjadi format datetime, sehingga dapat digunakan untuk analisis berbasis waktu.
*Menstandarkan format teks pada kolom kota dan channel agar memiliki penulisan yang konsisten, misalnya menghapus spasi berlebih, menyamakan penggunaan huruf besar/kecil, serta mengganti spasi dengan underscore pada nama channel.
*Melakukan data enrichment dengan menambahkan kolom baru:
bulan, yang diekstrak dari tanggal_order untuk memudahkan analisis tren bulanan.
kategori_harga, yang mengelompokkan transaksi menjadi kategori kecil, sedang, dan besar berdasarkan nilai total_harga.

- Langkah 3
Tahap Validate bertujuan untuk memastikan bahwa data hasil transformasi telah memenuhi standar kualitas yang ditetapkan sebelum disimpan ke database. Validasi dilakukan sebagai bentuk kontrol kualitas (quality assurance) agar hanya data yang valid dan konsisten yang diproses pada tahap berikutnya. 

Meskipun proses transformasi telah membersihkan data, masih diperlukan tahap validasi untuk memastikan seluruh aturan bisnis (business rules) telah terpenuhi. Langkah ini membantu mencegah data yang tidak valid masuk ke database, sehingga mengurangi risiko kesalahan analisis, laporan, maupun proses downstream lainnya. 

Aktivitas yang Dilakukan: 
*Memastikan tidak terdapat data duplikat, sehingga setiap transaksi hanya tercatat satu kali.
*Memastikan tidak ada missing values, agar seluruh data yang diperlukan tersedia.
*Memastikan tidak terdapat nilai transaksi negatif, karena transaksi dengan nilai negatif dianggap tidak valid.
*Memastikan tipe data kolom tanggal_order telah dikonversi menjadi datetime, sehingga dapat digunakan untuk analisis berbasis waktu.
*Memastikan nilai pada kolom channel telah distandarkan, sehingga tidak terdapat variasi penulisan yang berbeda untuk kategori yang sama.

## Load 
Tahap Load bertujuan untuk menyimpan data yang telah melalui proses transformasi dan validasi ke media penyimpanan yang siap digunakan oleh pengguna maupun sistem lain. Pada proyek ini, data bersih disimpan dalam format CSV sebagai simulasi proses loading, sedangkan pada implementasi di lingkungan produksi data biasanya dimuat ke data warehouse atau database seperti PostgreSQL, BigQuery, Snowflake, maupun Amazon Redshift. 

Aktivitas yang Dilakukan: 
*Memilih kolom yang diperlukan sebagai dataset akhir, termasuk kolom hasil transformasi seperti bulan dan kategori_harga.
*Menyimpan dataset bersih ke dalam file orders_clean.csv sebagai output utama pipeline.
*Membuat summary report dengan melakukan agregasi berdasarkan kategori produk untuk memperoleh informasi seperti:
Jumlah transaksi (total_orders),
Total pendapatan (total_revenue), dan
Rata-rata pendapatan (avg_revenue)

Menyimpan hasil agregasi ke dalam file summary_report.csv sehingga dapat digunakan untuk kebutuhan pelaporan atau analisis lanjutan.

## Orchestration 
Tools:
- Programming Language: Python 3
- Development Environment: Visual Studio Code
- Libraries: Pandas, NumPy
- Pipeline Orchestration: Python-based ETL Orchestrator (mengadopsi konsep Apache Airflow seperti task dependency, retry mechanism, logging, dan validation gate)

Pipeline saat ini dijalankan secara manual menggunakan Python.
Pada implementasi production menggunakan Apache Airflow, pipeline ini dapat dijadwalkan berjalan otomatis setiap hari pukul 06.00 menggunakan cron expression:
0 6 * * *

Artinya:
Menjalankan pipeline setiap hari
Waktu eksekusi pukul 06.00
Memproses data transaksi harian (daily batch processing)

DAG Flow 

<img width="315" height="694" alt="image" src="https://github.com/user-attachments/assets/eb47b733-a175-4b1e-89a8-752a89fffd12" />



## Error Handling 
- Skenario 1: File input tidak ditemukan
Penanganan:
 Sebelum membaca data, pipeline memeriksa apakah file raw_orders.csv tersedia. Jika file tidak ditemukan, sistem akan menghasilkan FileNotFoundError, mencatat kesalahan ke log, dan menghentikan pipeline.
Tujuan:
 Mencegah proses ETL berjalan tanpa sumber data yang valid.

- Skenario 2: Task gagal saat dieksekusi (Retry Mechanism)
Penanganan:
 Setiap task dijalankan menggunakan fungsi run_task(). Apabila terjadi error saat proses berlangsung, task akan dicoba kembali hingga 3 kali menggunakan mekanisme retry dengan exponential backoff (menunggu 2 detik, kemudian 4 detik, dan seterusnya).
Tujuan:
 Mengatasi kegagalan sementara (temporary failure) tanpa langsung menghentikan seluruh pipeline.

- Skenario 3: Validasi data gagal
Penanganan:
 Setelah transformasi, pipeline melakukan pemeriksaan kualitas data. Jika masih ditemukan data duplikat, missing values, harga negatif, atau tipe data yang tidak sesuai, maka task_validate() akan menghasilkan ValueError.
Pipeline akan berhenti sehingga data yang tidak memenuhi standar kualitas tidak akan dimuat ke output.
Tujuan:
 Menjamin hanya data yang telah lolos validasi yang dapat diproses ke tahap Load.

- Skenario 4: Pipeline gagal
Penanganan:
 Seluruh proses ETL dibungkus dalam blok try-except. Jika terdapat task yang gagal meskipun sudah dilakukan retry, pipeline akan:
*mencatat status FAILED ke file log,
*menampilkan pesan kesalahan kepada pengguna,
*menghentikan seluruh proses.
Tujuan:
 Memberikan informasi penyebab kegagalan dan mencegah pipeline menghasilkan output yang tidak lengkap atau tidak valid.

## Monitoring 
Keberhasilan pipeline dipantau melalui logging yang mencatat status setiap task selama proses ETL berlangsung. Setiap task memiliki status RUNNING, INFO, dan SUCCESS. Pipeline dinyatakan berhasil apabila:
*Seluruh task (Extract, Transform, Validate, Load, Report, dan Notify) selesai dengan status SUCCESS.
*Tidak terdapat status FAILED maupun FATAL pada log.
*Pipeline diakhiri dengan status COMPLETED yang menunjukkan seluruh workflow telah dieksekusi tanpa kesalahan.
