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

Tabel 1. deskripsi variabel _anime.csv_
| Variabel | Deskripsi |
| -------- | --------- |
| anime_id | Nomor unik mewakili judul *anime* |
| name     | Judul anime |
| genre    | Genre anime | 
| type     | Jenis penayangan |
| episodes | Episode pada anime (1 jika sebuah film) |
| rating   | Rating rata-rata anime, dari 10-1 |
| members  | Jumlah anggota komunitas yang bergabung pada group |

Tabel 2. deskripsi variabel _ratings.csv_
| Variabel | Deskripsi |
| -------- | --------- |
| user_id  | Nomor unik pengguna yang dihasilkan secara acak (tidak dapat diidentifikasi) |
| anime_id | Nomor unik mewakili anime yang pernah diberi rating oleh pengguna |
| rating   | Nilai rating yang diberikan pengguna antara 1 sampai 10 (-1 jika pengguna sudah menonton anime tetapi tidak memberi rating) |

### Exploratory Data Analysis

Pada tahap _EDA_ pertama akan dilakukan _Univariate analysis_ dimana analisis univariat merupakan teknik analisis data terhadap satu variabel secara mandiri.
Kita akan mengeksplorasi kumpulan data pada masing-masing variable yang penting dan akan digunakan untuk pelatihan nanti.

Tabel 3. jumlah fitur yang akan digunakan
| Fitur    | Jumlah | Keterangan |
| -------- | ------ | ---------- |
| anime    | 12.294 | total anime |
| users    | 73.515 | total pengguna |
| rating   | 11     | 11 jenis rating |
| genre    | 6      | 6 jenis genre |


![image](https://github.com/roamercodes/anime-recommender-system/assets/22432578/8d4c47fb-e206-43f8-bba1-83005d8d6e2c)

Gambar 1. Sebaran data fitur _type_ pada dataset anime.csv 

## Data Preprocessing

### Missing Value Handling

Data yang berisi nilai kosong (_missing value_) atau data _outlier_ (nilai diluar normal) akan berakibat buruk pada proses pemodelan, maka dari itu perlu dilakukan penanganan terhadap data tersebut. Pada fitur `rating` tepatnya pada data `anime_rating.csv` terdapat _outlier_ yakni terdapat nilai `-1` dari _outlier_ tersebut akan diganti kan `NaN value` dengan menggunakan `built-in` _function_ dari _Numpy_ yaitu `np.Nan` maka semua data yang bernilai `-1` akan digantikan.

Karena alasan keakuratan rekomendasi maka akan dilakukan penyaringan pada kumpulan data, berdasarkan Gambar 1 diketahui bahwa 3 teratas anime terbanyak ada pada tipe penayangan `TV` disusul `Ova` dan `Movie`, hanya ada dua tipe penayangan yang akan diambil yakni `TV` dan `Movie`, selain dari segi kumpulan data anime terbanyak isi konten juga perlu diperhatikan, walaupun `OVA` ada pada peringkat 2 terbanyak tetapi tipe `OVA` bukan bagian dari inti alur cerita anime `OVA` dipublikasikan dengan tujuan untuk memperjelas alur cerita [2]. Dengan proses penyaringan ini juga dapat mengurangi biaya komputasi yang cukup besar (nga percaya? coba sendiri!!). 

## Data Preparation

Ada perbedaan pada data yang digunakam pada *collaboarative filtering* dan *content-based filtering*, pada *collaboarative filtering* membutuhkan fitur `anime_id` `name`, `user_id` dan `rating` dimana kedua file `csv` akan digunakan sedangkan pada *content-based filtering* dibutuhkan fitur `name` dan `genre`.

### *Collaborative Filtering*

Pada *Collaborative Filtering* digunakan 2 dataset tersebut. Dataset akan digabungkan dengan mengacu pada kolom anime_id sehingga dikarenakan pada kedua dataset memiliki kolom anime_id tersebut. Dataset gabungan ini memiliki 7813727 baris dan 9 kolom.

Setelah digabungkan kemudian akan dicek apakah terdapat kolom yang bernilai null maupun baris yang duplikat. Ternyata terdapat kolom yang bernilai null yang kemudian akan diisi dengan whitespace agar tidak terhapus saat menghapus kolom null. Kemudian kolom rating_user yang bernilai -1 akan diubah menjadi null dan akan dihapus karena, baris kolom tersebut tidak memiliki informasi yang mewakili preferensi dari pengguna.

Ketika data sudah bersih dari Null value dan data duplikat kemudian akan diambil baris data yang miliki *members* pada kolom members dengan nilai lebih dari 250000. Hal ini dilakukan agar pengguna menerima rekomendasi anime yang berkualitas dan juga ditonton banyak orang. Jumlah dataset ini setelah difilter menjadi 1615445 baris dan 9 kolom. Kemudian data akan dibagi menjadi data latih dan data tes dengan rasio 90% : 10%. Data tes memiliki rasio yang lebih sedikit dikarenakan data yang tersedia cukup banyak jadi 10% data tes sudah termasuk banyak.

### *Content-based Filtering*

Dataset yang digunakan untuk *Content-based Filtering* adalah hanya dataset anime. Pertama dataset akan dicek apakah terdapat kolom yang bernilai null maupun baris yang duplikat. Ternyata terdapat data null pada kolom genre, type , dan rating. Kolom null tersebut tidak akan dihapus melainkan diisi dengan value whitespace karena kita membutuhkan kolom *name* dan *genre*.

Setelah itu kolom genre akan kita ekstrak datanya menjadi matriks 2 dimensi untuk setiap anime dikarenakan kolom genre berisi beberapa genre dari anime tersebut. Hal ini dilakukan untuk memudahkan pemetaan genre dan anime kedalam *Term Frequency-Inverse Document Frequency (TF-IDF) Vectorizer*








