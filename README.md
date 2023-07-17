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

Pada tahapan pertama setelah dilakukan persiapan pada data untuk model ini adalah membuat data menjadi format matriks _(user item matrix)_ dengan kolom matriks adalah pengguna baris matriks adalah anime dan isinya adalah rating. Kemudian dilakukan normalisasi pada data dengan mengurangkan nilai rata-rata setiap film. Kesamaan kosinus yang dihitung berdasarkan data yang dinormalisasi disebut _cosine similarity_ yang berpusat pada rata-rata. Setelah normalisasi, rating yang kurang dari rata-rata rating film mendapatkan nilai negatif, dan rating yang lebih dari rata-rata rating film mendapatkan nilai positif. Kemudian dilanjutkan dengan mengkalkulasi nilai _cosine similarity_ menggunakan metode _Pearson correlation_ [4]. Berikut ini adalah persamaan metode _pearson correlation similarity_:

$$ sim(k,i) = \frac{\sum_{u = 1}^m {(R_{u, k}-\overline{R_{k}})} {(R_{u, l}-\overline{R_{l}})} } {\sqrt\sum_{u=1}^m (R_{u, k}-\overline{R_{k}})^{2} \sqrt\sum_{u=1}^m (R_{u, l}-\overline{R_{l}})^{2}} $$

Dimana $sim(k,l)$ adalah nilai _similarity_ antara _item k_ dan _item_, $\overline{R_{k}}$ dan $\overline{R_{l}}$ adalah rating rata-rata pada _item k_ dan _item l_, $R_{u, k}$ dan $R_{u, l}$ adalah rating oleh pengguna _u_ kepada _item k_ dan _item l_ dan _m_ adalah jumlah total pengguna. Hasil perhitungan _cosine similarity_ menjadi dasar dalam pemberian rekomendasi kepada pengguna.
Tabel 4 dibawah ini merupakan hasil uji terhadap pengguna dengan id 101, dimana ada 5 top rekomendasi anime yang telah diurutkan berdasarkan prediksi rating tertinggi.

Tabel 4. Sampel hasil uji top 5 rekomendasi anime dengan _Item Based Collaborative Filtering_
 
| Anime          | Rating  |
| -------------- | ------- |
| Eikyuu Kazoku  | 3.232782 |
| Cutey Honey    | 3.192767 |
| Samurai Gun    | 3.170783 |
| Kujira no Chouyaku | 3.162018 |
| Omae Umasou da na | 3.15765 |


### *Model Based Collaborative Filtering*

Pada model dengan pendekatan _Collaborative Filtering_ lebih tepatnya _model based_ menggunakan arsitektur model _RecommenderNet_ yang dibangun menggunakan pustaka _TensorFlow_. Model ini menghitung skor kecocokan antara pengguna dan anime dengan teknik _embedding_, skor kecocokan ditetapkan dalam skala [0,1] dengan fungsi aktivasi _sigmoid_. Perbedaan pendekatan ini dengan content-based filtering adalah adanya pola preferensi pengguna yang dapat diprediksi, sehingga pengguna akan mendapatkan rekomendasi berdasarkan anime yang disukainya.
Kelebihan dari Collaborative Filtering ini adalah kemampuannya dalam menemukan pola preferensi pelanggan yang kompleks dan merekomendasikan produk berdasarkan preferensi serupa dari pelanggan lain. Namun kekurangan dari pendekatan ini adalah adanya masalah jika pelanggan baru atau produk baru tidak memiliki cukup interaksi untuk memberikan rekomendasi yang akurat.

Output dari pendekatan ini adalah Top-N anime yang mirip dengan anime yang sebelumnya sudah pengguna tonton dan diberi rating.

Tabel 5. Sampel hasil uji Top 10 Anime yang direkomendasikan dengan _model based collaborative filtering_

| No. | name                                                    | genre                                                         |
| --- | ------------------------------------------------------- | --------------------------------------------------------------|
|  1  | Tengen Toppa Gurren Lagann                              | Action, Adventure, Comedy, Mecha, Sci-Fi                      |
|  2  | Toradora!                                               | Comedy, Romance, School, Slice of Life                        |
|  3  | Sen to Chihiro no Kamikakushi                           | Adventure, Drama, Supernatural                                |
|  4  | Fullmetal Alchemist: Brotherhood                        | Action, Adventure, Drama, Fantasy, Magic, Military, Shounen   |
|  5  | Ano Hi Mita Hana no Namae wo Bokutachi wa Mada Shiranai | Drama, Slice of Life, Supernatural                            |
|  6  | Gintama                                                 | Action, Comedy, Historical, Parody, Samurai, Sci-Fi, Shounen  |
|  7  | Shigatsu wa Kimi no Uso                                 | Drama, Music, Romance, School, Shounen                        |
|  8  | Clannad: After Story                                    | Drama, Fantasy, Romance, Slice of Life, Supernatural          |
|  9  | Mononoke Hime                                           | Action, Adventure, Fantasy                                    |
|  10 | Cowboy Bebop                                            | Action, Adventure, Comedy, Drama, Sci-Fi, Space               |


### *Content Based Filtering*

Metode _content based filtering_ bekerja dengan menggunakan informasi profil preferensi pengguna terhadap item untuk dicari item yang mirip sebagai hasil rekomendasi. Salah satu pendekatan dalam membangun dengan metode ini dalah membuat profil pengguna dan item menggunakan konten yang telah diberi nilai. Pendekatan ini sebagian besar dirancang untuk merekomendasikan item berbasis teks, sehingga dapat berupa genre [5].

Kelebihan dari metode dengan pendekatan _Content Based Filtering_ yaitu dapat memberikan rekomendasi konten atau item yang belum pernah dirating sekalipun, kekurangan metode ini adalah tidak dapat merekomendasikan konten atau item bagi pengguna baru yang belum pernah melakukan aktivitas apapun. Berikut hasil dari uji rekomendasi yang akan ditampilkan pada Tabel 6 dimana merupakan Top 10 anime rekomendasi [6].

Tabel 6. Sampel hasil uji Top 10 Anime yang direkomendasikan dengan _content based filtering_

| No. | name                                                    | genre                                             | type  | rating |
| --- | ------------------------------------------------------- | ------------------------------------------------- | ----- | ------ |
|  1  | Wagamama☆Fairy Mirumo de Pon!                           | Adventure, Comedy, Drama, Fantasy, Kids, Magic...	| TV 	  |  7.47  |
|  2  | Denki-gai no Honya-san                                  | Comedy, Seinen, Slice of Life                     | TV    |  7.28  |
|  3  | Durarara!!                                              | Action, Mystery, Supernatural                     | TV    |  8.38  |
|  4  | Witch Craft Works                                       | Action, Fantasy, Magic, Seinen, Supernatural	     | TV    |  7.22  |
|  5  | Persona 3 the Movie 2: Midsummer Knight&#039;s...	      | Action, Fantasy, Seinen, Supernatural             | Movie |  7.90  |
|  6  | xxxHOLiC Kei                                            | Comedy, Drama, Mystery, Psychological, Superna... | TV    |  8.38  |
|  7  | Ike! Ina-chuu Takkyuubu                                 | Comedy, Ecchi, School, Sports                     | TV    |  7.70  |
|  8  | Toaru Majutsu no Index: Endymion no Kiseki	             | Action, Magic, Sci-Fi, Super Power                | Movie |  7.71  |
|  9  | Konjiki no Gash Bell!!: Mecha Vulkan no Raishuu         | Adventure, Shounen, Supernatural                  | Movie |  7.24  |
|  10 | Robin Hood no Daibouken                                 | Adventure, Historical, Romance                    | TV    |  7.25  |

## Evaluation
Evaluasi berguna untuk mengukur seberapa baik kinerja model pada tahap pengujian, hasil evaluasi pada masing-masing model akan dijelaskan seperti dibawah ini:

### *Memory Based | Item Based Collaborative Filtering*



`Presisi` Merupakan rasio prediksi benar positif dibandingkan dengan keseluruhan hasil yang diprediksi positif. Dalam konteks proyek ini, presisi mengukur seberapa baik model dalam memberikan rekomendasi anime dengan genre yang sama.

$$Presisi = {TP \\over TP + FP}.$$
