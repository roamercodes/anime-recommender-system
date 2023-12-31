# Laporan Proyek Machine Learning - Muhammad Kholilullah

## Project Overview

Hiburan di era digital kian melambung pesat, ada banyak sekali jenis hiburan yang ada didunia digital dan juga dapat menambah wawasan [1] salah satunya anime. Anime menjadi tontonan yang memiliki banyak peminat diseluruh dunia. Pada saat ini banyak platform-platform menyediakan anime untuk dikonsumsi oleh para penggemar anime, seperti Netflix, Amazon Prime, Bstation dsb. Pekembangan animme begitu sangat cepat, sehingga judul anime yang telah rilis sangatlah banyak dan cukup sulit untuk mencari anime yang sesuai dengan personalisasi setiap penggemar.
Dari permasalahan kesulitan menemukan anime yang sesuai dengan personalisasi penonton maka pada penelitian ini akan digunakan sistem rekomendasi sebagai solusi permasalahan tersebut. Ada beberapa pendekatan yang ada pada _Recommendation System_ yaitu Content Based Filtering_ dan _Collaborative Filtering_, pada penelitan ini akan digunakan kedua metode tersebut untuk membuat sistem rekomendasi anime.

![mashup_anime_collage_by_dinocojv-d8af5lu](https://github.com/roamercodes/anime-recommender-system/assets/22432578/9ca8c2dd-5f01-414c-85b1-d5eb0e97e4bf)

## Business Understanding

### Problem Statements

- Bagaimana merekomendasikan anime berdasarkan personalisasi penonton ?
- Bagaimana merekomendasikan anime berdasarkan kontent anime ?
- Bagaimana cara membuat sistem rekomendasi anime yang dapat memberikan rekomendasi yang relevan ?

### Goals

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
| Fitur    | Jumlah | Keterangan        |
| -------- | ------ | ----------------- |
| anime    | 12.294 | total anime       |
| users    | 73.515 | total pengguna    |
| rating   | 10     | 10 jenis rating   |
| genre    | 3264   | jumlah unik genre | 

Berdasarkan Tabel 3 dapat diketahui dari kumpulan data total jumlah anime adalah sebanyak 12.294, 73.515 pengguna, 3.264 genre dan 10 jenis rating dimana rating berkisar antara 1 hingga 10. Data pada Tabel 3 tersebut merupakan fitur yang dibutuhkan ketika ingin membangun sistem rekomendasi.

![image](https://github.com/roamercodes/anime-recommender-system/assets/22432578/8d4c47fb-e206-43f8-bba1-83005d8d6e2c)

Gambar 1. Sebaran data fitur _type_ pada dataset anime.csv 

Visualisasi perseberan data anime Gambar 1 diatas berdasarkan tipe-tipe penayangan yang ada pada kumpulan data _anime.csv_ dapat diketahui bawah data anime terbanyak urutan pertama berdasarkan tipe penayangan ialah anime dengan tipe penayangan TV yang kedua adalah OVA disusul Movie, Special dan terakhir adalah music.

## Data Preprocessing

### Missing Value Handling

Data yang berisi nilai kosong (_missing value_) atau data _outlier_ (nilai diluar normal) akan berakibat buruk pada proses pemodelan, maka dari itu perlu dilakukan penanganan terhadap data tersebut. Pada fitur `rating` tepatnya pada data `anime_rating.csv` terdapat _outlier_ yakni terdapat nilai `-1` dari _outlier_ tersebut akan diganti kan `NaN value` dengan menggunakan `built-in` _function_ dari _Numpy_ yaitu `np.Nan` maka semua data yang bernilai `-1` akan digantikan.

Karena alasan keakuratan rekomendasi maka akan dilakukan penyaringan pada kumpulan data, berdasarkan Gambar 1 diketahui bahwa 3 teratas anime terbanyak ada pada tipe penayangan `TV` disusul `Ova` dan `Movie`, hanya ada dua tipe penayangan yang akan diambil yakni `TV` dan `Movie`, selain dari segi kumpulan data anime terbanyak isi konten juga perlu diperhatikan, walaupun `OVA` ada pada peringkat 2 terbanyak tetapi tipe `OVA` bukan bagian dari inti alur cerita anime `OVA` dipublikasikan dengan tujuan untuk memperjelas alur cerita [2]. Dengan proses penyaringan ini juga dapat mengurangi biaya komputasi yang cukup besar. 

## Data Preparation

Ada perbedaan data yang digunakan pada  *item based collaboarative filtering*, *collaboarative filtering* dan *content-based filtering*. Pada *item based collaboarative filtering* digunakan fitur `user_id` `rating_x` dan `name` _(anime name)_ untuk menghitung skor kemiripan menggunakan _cosine similarity_. Pada *collaboarative filtering* membutuhkan fitur `anime_id`, `name`, `user_id` dan `rating_x` juga membutuhkan kumpulan data yang isinya hanya dengan anime saja untuk dilakukan kecocokan ketika ingin melakukan tes rekomendasi. Sedangkan pada *content-based filtering* dibutuhkan hasil perhitungan `cosine similarity` yang dimana telah dilakukan pada model *item based collaboarative filtering*.

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

Pada perisiapan data untuk _content based filtering_ dibutuhkan hasil derajat kesamaan dengan teknik _cosine similarity_, karena sebelumnya pada model _item based collaborative_ juga menghitung _cosine similarity_ maka hasil tersebut akan digunakan juga pada model ini.

## Modeling

Berdasarkan latar belakang domain proyek telah dijelaskan bawwa pada penelitian ini menggunakan 3 metode pendekatan dalam membuat sistem rekomendasi anime.

### *Memory Based | Item Based Collaborative Filtering*

Pendekatan dengan _item based collaborative filtering_ memberikan rekomendasi berdasarkan kemiripan antar item. Pada pendekatan ini didasari atas adanya kemiripan antara pemberian rating terhadap suatu item dengan item yang pernah dirating pengguna lain. Item yang telah dirating oleh pengguna akan menjadi patokan untuk mencari item lainnya yang berkorelasi dengan item yang telah dirating pengguna.

Kelebihan _item based collaborative filtering_ ada;ah proses implementasi yang mudah, mudah menambahkan data-data baru tidak perlu menpertimbangkan content item yang direkomendasikan, skala yang baik dengan co-rated item sedangkan kekurangan _item based collaborative filtering_ adalah adalah algoritma ini bergantung pada rating dari pengguna, menurun-nya performa jika data jarang, skalabilitas yang terbatas pada dataset yang besar [3].

Tahapan langkah implementasi metode _item based collaborative filtering_ :
1. Membuat _user item matrix_
2. Melakukan normalisasi data 
3. Menerapkan _cosine similarity_
4. Membuat tes rekomendasi

Pada tahapan pertama setelah dilakukan persiapan pada data untuk model ini adalah membuat data menjadi format matriks _(user item matrix)_ dengan kolom matriks adalah pengguna baris matriks adalah anime dan isinya adalah rating. Kemudian dilakukan normalisasi pada data dengan mengurangkan nilai rata-rata setiap film. Kesamaan kosinus yang dihitung berdasarkan data yang dinormalisasi disebut _cosine similarity_ yang berpusat pada rata-rata. Setelah normalisasi, rating yang kurang dari rata-rata rating film mendapatkan nilai negatif, dan rating yang lebih dari rata-rata rating film mendapatkan nilai positif. Kemudian dilanjutkan dengan mengkalkulasi nilai skor kemiripan menggunakan metode _Pearson correlation_ [4]. Berikut ini adalah persamaan metode _pearson correlation similarity_:

$$ sim(k,i) = \frac{\sum_{u = 1}^m {(R_{u, k}-\overline{R_{k}})} {(R_{u, l}-\overline{R_{l}})} } {\sqrt\sum_{u=1}^m (R_{u, k}-\overline{R_{k}})^{2} \sqrt\sum_{u=1}^m (R_{u, l}-\overline{R_{l}})^{2}} $$

Dimana $sim(k,l)$ adalah nilai _similarity_ antara _item k_ dan _item_, $\overline{R_{k}}$ dan $\overline{R_{l}}$ adalah rating rata-rata pada _item k_ dan _item l_, $R_{u, k}$ dan $R_{u, l}$ adalah rating oleh pengguna _u_ kepada _item k_ dan _item l_ dan _m_ adalah jumlah total pengguna.

Hasil perhitungan _cosine similarity_ menjadi dasar dalam pemberian rekomendasi kepada pengguna.
Tabel 4 dibawah ini merupakan hasil uji terhadap pengguna dengan id 101, dimana ada 5 top rekomendasi anime yang telah diurutkan berdasarkan prediksi rating tertinggi.

Tabel 4. Sampel hasil uji top 5 rekomendasi anime dengan _Item Based Collaborative Filtering_
 
| Anime              | Rating   |
| ------------------ | -------- |
| Eikyuu Kazoku      | 3.232782 |
| Cutey Honey        | 3.192767 |
| Samurai Gun        | 3.170783 |
| Kujira no Chouyaku | 3.162018 |
| Omae Umasou da na  | 3.15765  |

### *Model Based Collaborative Filtering*

Pada model dengan pendekatan _Collaborative Filtering_ lebih tepatnya pada _model based_ menggunakan arsitektur model _RecommenderNet_ yang dibangun menggunakan pustaka _TensorFlow_. Model ini menghitung skor kecocokan antara pengguna dan anime dengan teknik _embedding_, skor kecocokan ditetapkan dalam skala [0,1] dengan fungsi aktivasi _sigmoid_. Perbedaan pendekatan ini dengan content-based filtering adalah adanya pola preferensi pengguna yang dapat diprediksi, sehingga pengguna akan mendapatkan rekomendasi berdasarkan anime yang disukainya.
Kelebihan dari Collaborative Filtering ini adalah kemampuannya dalam menemukan pola preferensi pelanggan yang kompleks dan merekomendasikan produk berdasarkan preferensi serupa dari pelanggan lain. Namun kekurangan dari pendekatan ini adalah adanya masalah jika pelanggan baru atau produk baru tidak memiliki cukup interaksi untuk memberikan rekomendasi yang akurat.

Tahapan langkah implementasi metode _model based collaborative filtering_ :
1. _Data preparation_
   - Melakukan _encoding user_id_ dan _anime_id_
   - Membagi data latih dan data validasi dengan rasio 80:20
2. Membuat arsitektur model
   - Menerapkan arsitektur model menggunakan _RecommenderNet_
   - Menginisialisasikan model dengan 3 parameter yakni jumlah pengguna, jumlah anime dan jumlah _embedding_
   - Meng-_compile_ model menggunakan _binary crossentropy_ untuk menghitung _loss_, _Adam (Adaptive Moment Estimation)_ sebagai _optimizer_, dan _root mean squared error (RMSE)_ sebagai metriks evaluasi. 
4. Menginisialisasikan _callback_ teknik yang digunakan adalah _model check point_ dan memonitor _validation rmse_.
5. Melakukan proses _training_ dengan 5 iterasi.
6. Visualisasi hasil proses _training_.
7. Membuat tes rekomendasi.

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

Tahapan langkah implementasi metode _content based filtering_ :
1. Mendatkan hasil _cosine similarity_
2. Membuat _dataframe_ berdasarkan perhitungan _cosine similarty_
3. Membuat tes rekomendasi

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

Metriks evaluasi yang akan digunakan pada metode ini yaitu _MAE_ dimana _mean absolute error (MAE)_ mengitung rata-rata dari _error_ yang di absolutkan. Dimana _error_ merupakan selisih dari nilai rating sebenarnya dengan nilai rating hasil prediksi. Persamaan ini termasuk jenis _statistical accuracy metrics_ dimana MAE akan menghitung nilai rata-rata selisih Antara nilai prediksi dengan nilai yang sebenarnya [5].

$$ MAE = \frac{\sum_{u = 1}^n {(P_{u, k})} {(R_{u, k})} } {n} $$

Dimana $P_{u, k}$ adalah Prediksi rating pengguna _u_ untuk _item k_, $R_{u, k}$ adalah Nilai rating yang diberikan pengguna _u_ untuk _item k_, dan _N_ adalah Jumlah data.

Disini akan mencoba mengevaluasi terhadap user yang telah menonton sebuah anime dan melihat hasi prediksi ratingnya. Berdasarkan hasil uji pada tabel 4 yang menggunakan id pengguna yaitu `101` bahwa pengguna tersebut telah menonton dan merating anime `Naruto` dengan rating 3.279614 kemudian hasil skor kemiripan yang didapat adalah 1.000000 dan mendapatkan hasil prediksi rating yakni 3.136683.
Maka dari hasil tersebut bahwa bisa diketahui bahwa diperoleh hasil _MAE_ sebesar 0.1429.

| $$MAE = \sqrt {\frac{(3.279614, 3.136683) }{1} }$$ |
| -------------------------------------------------- |
| $$MAE = 0.1429$$                                   |

MAE sebesar 0.1429 berarti rata-rata kesalahan absolut antara rekomendasi yang diberikan oleh model dan preferensi sebenarnya dari pengguna adalah 0.1429. Semakin mendekati nol, semakin akurat model dalam memberikan rekomendasi.

### *Model Based Collaborative Filtering*

Pada pendekatan dengan metode ini metriks evaluasi yang di gunakan ialah _RMSE (Root Mean Squared Error)_. _Error_ diperoleh berdasarkan nilai selisih antara hasil prediksi item terhadap data. Hasil dari perhitungan _RMSE_ terkecil berarti baik, semakin kecil nilai _RMSE_ yang di dapat maka kualitas akurasi rekomendasi semakin baik [7].

| $$RMSE = \sqrt {\frac{1}{N} \sum_{i=1}^{N} (\hat{y_{i}} - y_{i})^2}$$ |
| --------------------------------------------------------------------- |

dimana $\hat{y}$ merupakan rating prediksi _y_ merupakan rating aktual dan _n_ merupakan jumlah data.

Contoh perhitungan :

| $$RMSE = \sqrt {\frac{(0.392827, 0.212526)^2 + (0.283836, 0.489392)^2 + (0.961220, 0.987328)^2 }{3} }$$   |
| --------------------------------------------------------------------------------------------------------- |
| $$RMSE = 0.158580$$                                                                                       |

![RMSE PLOT](https://github.com/roamercodes/anime-recommender-system/assets/22432578/67a86cde-49df-42bb-bc40-6ddbbab14ffc)

Gambar 3. Visualisasi hasil metriks _RMSE_ pada _model based collaborative filtering_

Nilai selisih yang didapatkan antara nilai aktual dan prediksi yang dijumlahkan akan dikuadratkan kemudian hasil perjumlahan tersebut dibagi dengan data ke _n_ untuk menentukan nilai rata-ratanya. Operasi perhitungan akar terhadap nilai rata-rata dilakukan agar satuan nilai _RMSE_ prediksi sama dengan satuan nilai aktual, sehingga kemudian akan didapat nilai _RMSE_ yang mendekati nol. Hasil _RMSE_ pada penelitian ini mendapatkan nilai sebesar 0.2051 dengan proses iterasi pelatihan _(training)_ dilakukan sebanyak 5 _epoch_ walaupun sebenarnya dapat ditingkatkan lagi jika merujuk pada hasil visualisasi _RMSE_ yang cenderung masih turun, namun karena keterbatasan komputasi proses pelatihan hanya mampu di 5 iterasi. 
RMSE sebesar 0.2051 berarti rata-rata kesalahan prediksi model dalam mengukur preferensi pengguna adalah 0.2051 yang berarti juga nilai tersebut sudah cukup baik untuk mendekati nol.

### *Content Based Filtering*

Pada pendekatan ini untuk metriks evaluasi yang di gunakan adalah precision, recall dan accuracy yang dapat dihitung dengan menggunakan rumus seperti dibawah ini. Pada Tabel 5 merupakan hasil rekomendasi dari _content based filtering_ dimana ada 10 Top-N anime rekomendasi dengan input anime seperti pada Tabel 7 dibawah, tingkat keberhasilan rekomendasi akan dikatakan _correct_ jika ada setidaknya 1 kategori genre yang ada pada genre masukan anime. 

Tabel 7 Tes Masukan Anime Untuk _Conten Based Filtering_
| No. | name                                                    | genre                                             | 
| --- | ------------------------------------------------------- | ------------------------------------------------- |
|  1  | Hunter X Hunter (2011)                                  | Action, Adventure, Shounen, Super Power          	|

Tabel 8 Perbandingan genre masukan dan rekomendasi

| No. | name                                              | genre                                             | genre input                             | hasil rekomendasi |
| --- | ------------------------------------------------- | ------------------------------------------------- | --------------------------------------- | ----------------- |
|  1  | Wagamama☆Fairy Mirumo de Pon!                     | Adventure, Comedy, Drama, Fantasy, Kids, Magic...	| Action, Adventure, Shounen, Super Power | Correct           |
|  2  | Denki-gai no Honya-san                            | Comedy, Seinen, Slice of Life                     | Action, Adventure, Shounen, Super Power | Uncorrect         |
|  3  | Durarara!!                                        | Action, Mystery, Supernatural                     | Action, Adventure, Shounen, Super Power | Correct           |
|  4  | Witch Craft Works                                 | Action, Fantasy, Magic, Seinen, Supernatural	     | Action, Adventure, Shounen, Super Power | Correct           |
|  5  | Persona 3 the Movie 2: Midsummer Knight&#039;s...	| Action, Fantasy, Seinen, Supernatural             | Action, Adventure, Shounen, Super Power | Correct           |
|  6  | xxxHOLiC Kei                                      | Comedy, Drama, Mystery, Psychological, Superna... | Action, Adventure, Shounen, Super Power | Uncorrect         |
|  7  | Ike! Ina-chuu Takkyuubu                           | Comedy, Ecchi, School, Sports                     | Action, Adventure, Shounen, Super Power | Uncorrect         |
|  8  | Toaru Majutsu no Index: Endymion no Kiseki	       | Action, Magic, Sci-Fi, Super Power                | Action, Adventure, Shounen, Super Power | Correct           |
|  9  | Konjiki no Gash Bell!!: Mecha Vulkan no Raishuu   | Adventure, Shounen, Supernatural                  | Action, Adventure, Shounen, Super Power | Correct           |
|  10 | Robin Hood no Daibouken                           | Adventure, Historical, Romance                    | Action, Adventure, Shounen, Super Power | Correct           |

Pada Tabel 8 dapat diketahui bahwa dari 10 hasil rekomendasi yang terdata benar atau _correct_ sebanyak 7 dan dari 10 hasil rekomendasi yang terdata salah atau _uncorrect_ sebanyak 3.
Pada pendekatan ini untuk metriks evaluasi yang di gunakan yaitu _precision_, _recall_ dan _accuracy_ yang dapat dihitung dengan menggunakan rumus seperti dibawah ini :

- Akurasi adalah hasil prediksi yang benar dari keseluruhan data uji.

| $$Accuracy = \frac {TP + TN} { TP + FP + FN + TN }$$ |
| ---------------------------------------------------- |

- Presisi adalah prediksi rasio TP dibanding keseluruhan prediksi positif.

| $$Precision = \frac {TP} { TP + FP }$$ |
| -------------------------------------- |

- Recall adalah prediksi rasio TP dibanding keseluruhan data yang benar.

| $$Recall = \frac {TP} {TP + FN }$$ |
| ---------------------------------- |

Apabila dilakukan implementasi maka :

_Precision_:

| $$\text{{Precision}} = \frac{{\text{{7}}}}{{\text{{7}} + \text{{3}}}} = \text{{0,7}}$$ |
| -------------------------------------------------------------------------------------- |

_Recall_:

| $$\text{{Recall}} = \frac{{\text{{7}}}}{{\text{{7}} + \text{{0}}}} = \text{{1}}$$ |
| -------------------------------------------------------------------------------------- |

_Accuracy_:

| $$\text{{Accuracy}} = \frac{{\text{{7}} + \text{{0}}}}{{\text{{0}} + \text{{3}} + \text{{0}} + \text{{0}}}} = \text{{7}}$$  |
| ----------------------------------------------------------------------------------------------------------------------------- |

Berdasarkan hasil perhitungan diatas hasil dari _precision_ mendapatkan 70%, _recall_ mendatkan 100% dan _accuracy_ mendapatkan 70%.

## Conclusion

Sesuai dengan apa yang dijelaskan pada tujuan proyek ini yaitu membuat sistem rekomendasi anime menggunakan 3 metode pendekatan yang berbeda. Pada model pertama yaitu _item based collaborative filtering_ mendapatkan nilai _MAE_ sebesar 0.1429 dan pada model yang kedua yaitu _model based collaborative filtering_ mendapatkan nilai _RMSE_ sebesar 0.2051 dan pada model yang terakhir yakni _content based filtering_ mendapatkan hasil _precision_ mendapatkan 70%, _recall_ mendatkan 100% dan _accuracy_ mendapatkan 70%. Dari sedikit tinjauan tersebut maka dalam proyek ini dirasa telah mendapatkan hasil yang sesuai ekspetasi walaupun masih belum maksimal dalam proses pelatihan ataupun proses pemilihan dan penyaringan fitur.

## Recommendations

Saran yang dapat dipertimbangkan untuk proyek penelitian selanjutnya adalah mecoba menggunakan seluruh data dimana pada proyek ini masih banyak dilakukan pemangkasan data. Kemudian mencoba menyaring lagi fitur yang mungkin dapat meningkatkan kualitas data latih. Melakukan iterasi yang lebih banyak jika menggunakan metode seperti _model based collaborative filtering_ karena pada proyek ini hanya menggunakan 5 iterasi saja. Terakhir menerapkan pendekatan dengan metode _hybird_ misal _demographic filtering_ untuk merekomendasikan anime pada pengguna baru dan _model based_ untuk rekomendasi anime kepada pengguna secara _personalized_.

## Daftar Pustaka

[1]	Iklil jayaperwira, Agung Toto Wibowo, and Dade Nurjanah, “Anime Rekomendasi Menggunakan Collaborative Filtering,” E-Proceeding Eng., vol. 3, pp. 3431–3440, 2023.

[2]	U. S. & T. KOMPUTER, “Video animasi orisinal.” https://p2k.stekom.ac.id/ensiklopedia/Video_animasi_orisinal (accessed Jul. 14, 2023).

[3]	E. A. Laksana, “COLLABORATIVE FILTERING DAN APLIKASINYA,” J. Ilm. Teknol. Infomasi Terap., vol. 1, no. 1, Art. no. 1, Dec. 2014, doi: 10.33197/jitter.vol1.iss1.2014.44.

[4]	B. Prasetyo, H. Haryanto, S. Astuti, E. Z. Astuti, and Y. Rahayu, “Implementasi Metode Item-Based Collaborative Filtering dalam Pemberian Rekomendasi Calon Pembeli Aksesoris Smartphone,” J. Eksplora Inform., vol. 9, no. 1, Art. no. 1, Sep. 2019, doi: 10.30864/eksplora.v9i1.244.

[5]	“Sistem Rekomendasi Film Menggunakan Metode Hybrid Collaborative Filtering Dan Content-based Filtering | Arfisko | eProceedings of Engineering.” https://openlibrarypublications.telkomuniversity.ac.id/index.php/engineering/article/view/18066 (accessed Jul. 17, 2023).

[6]	A. E. Wijaya and D. Alfian, “SISTEM REKOMENDASI LAPTOP MENGGUNAKAN COLLABORATIVE FILTERING DAN CONTENT-BASED FILTERING”.

[7]	A. S. Dharma, R. B. B. A. Hutasoit, and R. R. Pangaribuan, “Sistem Rekomendasi Menggunakan Item-based Collaborative Filtering pada Konten Artikel Berita,” J. Nas. Tek. Inform. Dan Elektro JURNALTIO, vol. 2, no. 01, Art. no. 01, Apr. 2021.

