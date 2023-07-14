# Laporan Proyek Machine Learning - Muhammad Kholilullah

## Project Overview

Hiburan di era digital kian melambung pesat, ada banyak sekali jenis hiburan yang ada didunia digital dan juga dapat menambah wawasan [1] salah satunya anime. Anime menjadi tontonan yang memiliki banyak peminat diseluruh dunia. Pada saat ini banyak platform-platform menyediakan anime untuk dikonsumsi oleh para penggemar anime, seperti Netflix, Amazon Prime, Bstation dsb. Pekembangan animme begitu sangat cepat, sehingga judul anime yang telah rilis sangatlah banyak dan cukup sulit untuk mencari anime yang sesuai dengan personalisasi setiap penggemar.
Dari permasalahan kesulitan menemukan anime yang sesuai dengan personalisasi penonton maka pada penelitian ini akan digunakan sistem rekomendasi sebagai solusi permasalahan tersebut. Ada beberapa pendekatan yang ada pada _Recommendation System_ yaitu Content Based Filtering_ dan _Collaborative Filtering_, pada penelitan ini akan digunakan kedua metode tersebut untuk membuat sistem rekomendasi anime.

gambar 1. proyek cover

![mashup_anime_collage_by_dinocojv-d8af5lu](https://github.com/roamercodes/anime-recommender-system/assets/22432578/9ca8c2dd-5f01-414c-85b1-d5eb0e97e4bf)

## Business Understanding

### Problem Statements

- Bagaimana membuat model sistem rekomendasi anime ?
- Bagaimana merekomendasikan anime berdasarkan personalisasi penonton ?
- Bagaimana merekomendasikan anime berdasarkan kontent anime ?
- Bagaimana cara membuat sistem rekomendasi anime yang dapat memberikan rekomendasi yang relevan ?

### Goals

- Menghasilkan sistem rekomendasi anime.
- Menggunakan pendekatan _Collaborative Filtering_ yakni _memory based_ dan _model based_.
- Menggunakan pendekatan _Content Based Filtering_ untuk merekomendasikan anime kepada penonton dengan membuat sistem rekomendasi *anime*.
- Membuat model sistem rekomendasi anime yang dapat merekomendasikan *anime* yang relevan.

### Solution Approach

Untuk mencapai tujuan penelitian ini akan digunakan dua pendekatan dalam membuat sistem rekomendasi, yaitu :

- *Item Based Collaborative filtering*, yang bekerja dengan cara menghitung kesamaan antara masing-masing item.
-  *Model Based Collaborative filtering*, yang mengekstrak informasi dari kumpulan data kemudian menggunakan model probabilitas untuk membuat rekomendasi.
- *Content-based filtering*, yang mempelajari profil minat pengguna baru berdasarkan data dari objek yang telah dinilai pengguna.

## Data Understanding

Setdata yang digunakan pada penelitian ini berasal dari situs Kaggle dengan judul [*Anime Recommendations Database*](https://www.kaggle.com/datasets/CooperUnion/anime-recommendations-database). Kumpulan data ini berisi informasi data preferensi pengguna dari 73.516 pengguna di 12.294 anime. Setiap pengguna dapat menambahkan anime ke daftar lengkap mereka dan memberikannya peringkat dan kumpulan data ini adalah kompilasi dari peringkat tersebut.

**Anime.csv**

| Variabel | Deskripsi |
| -------- | --------- |
| anime_id | Nomor unik mewakili judul *anime* |
| name     | Judul anime |
| genre    | Genre anime | 
| type     | Jenis penayangan |
| episodes | Episode pada anime (1 jika sebuah film) |
| rating   | Rating rata-rata anime, dari 10-1 |
| members  | Jumlah anggota komunitas yang bergabung pada group |


**Rating.csv**

| Variabel | Deskripsi |
| -------- | --------- |
| user_id  | Nomor unik pengguna yang dihasilkan secara acak (tidak dapat diidentifikasi) |
| anime_id | Nomor unik mewakili anime yang pernah diberi rating oleh pengguna |
| rating   | Nilai rating yang diberikan pengguna antara 1 sampai 10 (-1 jika pengguna sudah menonton anime tetapi tidak memberi rating) |

### Exploratory Data Analysis

 Pada dataset anime.csv (yang selanjutnya akan ditulis dataset anime) memiliki kolom type yang berisi data media yang digunakan untuk penayangan anime tersebut

 ![Image](https://github.com/IbraHarsye/anime-recommendation-system/assets/56579824/27383eab-d5bd-4879-907b-f3dfe8c986fe)
 Gambar 1. Kolom type pada dataset anime

 Selanjutnya pada dataset rating.csv (yang selanjutnya akan disebut dataset rating) terdapat persebaran rating dari user. Persebaran data terbanyak berada diantara nilai 7, 8, dan 9.
 
![Image](https://github.com/IbraHarsye/anime-recommendation-system/assets/56579824/83c4a31c-8e90-4e89-a913-67243232872a)
 Gambar 2. Kolom rating pada dataset rating

Tabel. 1 Data statistik kolom rating pada dataset rating

|         | rating        |
|-------- |-------------- |
| mean    | 6.144030e+00  |
| std     | 3.727800e+00  |
| min     | -1.000000e+00	|
| 25%     | 6.000000e+00	|
| 50%     | 7.000000e+00  |
| 75%     | 9.000000e+00  |
| max     | 1.000000e+01	|

Pada dataset rating dapat dilihat rating minimal adalah -1 dan maksimal adalah 10. data yang bernilai -1 ini merupakan nilai yang diberikan jika seorang pengguna menonton sebuah *anime* dan belum memberikan rating. Baris data ini tidak akan digunakan dan akan dihapus nanti karena, tidak memberikan informasi tentang preferensi pengguna tersebut.

## Data Preparation










