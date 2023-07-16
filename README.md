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

Ada perbedaan pada data yang digunakam pada *collaboarative filtering* dan *content-based filtering*, pada *collaboarative filtering* membutuhkan fitur `anime_id` `name`, `user_id` dan `rating` dimana kedua file `csv` akan digunakan sedangkan pada *content-based filtering* dibutuhkan fitur `name`, `genre` dan hasil perhitungan `cosine similarity`.

### *Memory Based | Item Based Collaborative Filtering*

Pada *Item Based Collaborative Filtering* menggunakan dua kumpulan data dan digabung.
Tahapan pada persiapan data untuk _IBCF (item based collaborative Filtering)_ ialah:

- Menghirung skor _item similiarity_ berdasarkan semua rating pengguna.
- Mengidentifikasi item teratas yang paling mirip dengan item yang diminati.
- Menghitung skor rata-rata untuk item yang paling mirip oleh pengguna.
- Memberi rating item berdasarkan skor dan item teratas untuk direkomendasikan.

### *Model Based | Collaborative Filtering*

Pada persiapan data untuk _model based collaborative filtering_ juga menggunakan kedua file csv. Namun ada pemangkasan data cukup banyak pada data yang akan digunakan untuk model ini karena keterbatasan komputasi yang dimiliki. Pada persiapan data untuk semua model, total jumlah data adalah 6.271.201 kemudian dilakukan penyortiran data berdasarkan `members` yang lebih dari 300.000, sehingga hasil total data menjadi 1.521.935 baris.

Tahapan pada persiapan data untuk _model based collaborative filtering)_ ialah:

- Memangkas data berdasarkan `members` lebih dari 300.000
- _Splitting_ atau pemisahan data latih dan data validasi dengan komposisi 80:20.

### *Content-based Filtering*

Pada perisiapan data untuk _content based filtering_ dibutuhkan hasil derajat kesamaan dengan teknik _cosine similarity_, karena sebelumnya pada model _IBCF_ juga menghitung _cosine similarity_ maka hasil tersebut akan digunakan juga pada model ini.

## Modeling

Berdasarkan latar belakang domain proyek telah dijelaskan bawwa pada penelitian ini menggunakan 3 metode pendekatan dalam membuat sistem rekomendasi anime.

### *Memory Based | Item Based Collaborative Filtering*

Pendekatan dengan _item based collaborative filtering_ memberikan rekomendasi berdasarkan kemiripan antar item. Pada pendekatan ini didasari atas adanya kemiripan antara pemberian rating terhadap suatu item dengan item yang pernah dirating pengguna lain. Item yang telah dirating oleh pengguna akan menjadi patokan untuk mencari item lainnya yang berkorelasi dengan item yang telah dirating pengguna.

Kelebihan _item based collaborative filtering_ ada;ah proses implementasi yang mudah, mudah menambahkan data-data baru tidak perlu menpertimbangkan content item yang direkomendasikan, skala yang baik dengan co-rated item sedangkan kekurangan _item based collaborative filtering_ adalah adalah algoritma ini bergantung pada rating dari pengguna, menurun-nya performa jika data jarang, skalabilitas yang terbatas pada dataset yang besar [3].

Pada tahapan pertama setelah dilakukan persiapan pada data untuk model ini adalah membuat data menjadi format matriks _(user item matrix)_ dengan kolom matriks adalah pengguna baris matriks adalah anime dan isinya adalah rating. Kemudian dilakukan normalisasi pada data dengan mengurangkan nilai rata-rata setiap film. Kesamaan kosinus yang dihitung berdasarkan data yang dinormalisasi disebut _cosine similarity_ yang berpusat pada rata-rata. Setelah normalisasi, rating yang kurang dari rata-rata rating film mendapatkan nilai negatif, dan rating yang lebih dari rata-rata rating film mendapatkan nilai positif. Kemudian dilanjutkan dengan mengkalkulasi nilai _cosine similarity_ menggunakan metode _Pearson correlation_.

$$ ![\Large sim(k,i) = \sum_{u=1}^m (R_{u, k}-\overline{R_{k}}) (R_{u, i}-\overline{R_{i}}) \over {\sqrt \sum_{u=1}^m (R_{u, k}-\overline{R_{k}})^{2}} {\sqrt \sum_{u=1}^m (R_{u, i}-\overline{R_{i}})^{2}}  $$

$$ ![\Large sim(k,i) = \frac{\sum_{u=1}^m (R_{u, k}-\overline{R_{k}}) (R_{u, i}-\overline{R_{i}}) \sqrt \sum_{u=1}^m (R_{u, k}-\overline{R_{k}})^{2} \sqrt \sum_{u=1}^m (R_{u, i}-\overline{R_{i}})^{2}} $$
 
$$ cosine similarity = {{A . B} \over {||A|| ||B||}} $$






