1) Desain Kuesioner
- Dari 30 Pertanyaan berbasis skala Likert 1-5 kemudian dipecah menjadi 2 Model masing-masing model 15 pertanyaan:
- Kecemasan diambil dari pertanyaan yang berkaitan rasa takut, khawatir, cemas terhadap masa depan, keraguan dan perasaan tidak pasti yaitu pertanyaan Q1,Q2,Q3,Q4,Q6,Q7,Q11,Q12,Q18,Q19,Q20,Q21,Q23,Q25
- Setiap Model memiliki 5 apek (variabel x)

2) Tahap Pre-processing
a. Reverse Coding
- Reverse scoring digunakan agar arah penilaian konsisten, dikarenakan penelitian saya meneliti terkait Stress dan Kecemasan maka pertanyaan bersifat Positif dibalik (reverse)
- Jika nilai Likert 5 = sangat setuju (skor sebenarnya harus rendah)  maka 5=1
- Pertanyaan yang harus di Reverse 'Q1', 'Q2', 'Q3', 'Q19', 'Q20', 'Q21', 'Q25'
- Prinsipnya:
  "Sekor tinggi = menunjukkan anxiety tinggi" / "Sekor Rendah = anxiety rendah"

b. Penjumlahan & Cut-off
- Setelah direverse Hitung skor per-aspek
- Mengategorisasikan dengan menggunakan Summated Rating Scale (Likert) + Cut-Off Empiris
- Disisni saya membagikan hasil dari penjumlahan 4 aspek (On Human Nature, On Knowledge, On Ethic dan On Reality) berbasis EAQ dengan jumlah item keseluruhan 10 item
  > Total min : 10x1 = 10
  > Total max : 10x5 = 50
  > Dibagi menjadi 3 tingkatan
  - Mild : 10-23
  - Moderate : 24-34
  - Severe : 35-50
- Disini saya tidak menggabungkan aspek Nature of God dikarenakan aspek tersebut setelah ditelaah kembali tidak termasuk Existential Anxiety Quessionaires (EAQ) dikarenakan lebih mempresentasikan nilai tauhid, filsafar al-wujud dan religious yang mana tidak dapat langsung diukur menggunakan EAQ.
- Karena EAQ mengukur kecemasan Existential yang mengarah kepada kematian, makna hidup, rasa bersalah, isolasi sosial, identitas diri.
- Maka saya perlakukan aspek ini sebagai variabel terpisah tetap menggunakan skala likert tapi tidak dijumlahkan bersamaan item EAQ. Jadi bisa melihat pengaruh spiritualnya.

- Saya juga menambahkan 2 fitur tambahan yaitu Gender dan Prodi apakah 2 fitur ini berpengaruh terhadap label target klasifikasi
- Saya melakukan encoder unduk mengubah string prodi dan gender menjadi nilai numerik secara otomatis
  
c. Normalisasi
- Saya melakukan normalisasi untuk menyetarakan skala semua variable input. Karena setiap aspek memiliki jumlah item berbeda, yang mana rentang skor secara otomatis akan berjumlah berbeda.
- Ex:
  > Nature of God = 5 item (5-25)
  > 4 aspek berbasis EAQ = 10 item (10-50)
- Kalau tidak dinormalisasikan algoritma svm bisa menganggap fitur dengan range besar lebih dominan, padahal bobotnya harus setara.
- Menggunakan Min-Max Normalization (yaitu 0 sampai 1) agar SVM fokus pada pola hubungan antar fitur bukan besar kecil skala.

3) Data Split
- Split dataset menjadi Train dan Test
  > 80% data train dan 20% data test
-Tujuannya agar menghindari overfitting dan mengecek generalization

4) SMOTE
- Saya menggunakan metode SMOTE untuk menangani class imbalance agar model tidak bias terhadap kelas mayoritas. Dengan membuat contoh sintetis di kelas minoritas, model dapat belajar batas pemisah (decision boundary) dengan lebih baik.
- Melihat hasil dari hasil EAQ Anxiety_Level
  > Mild     : 75
  > Moderate : 69
  > Severe   : 2
- Melihat data saya kelas Severe hanya ada 2. Kalau tidak ditangani, model akan selalu gagal mendeteksi Severe.
- Karena itu saya gunakan SMOTE agar menambah data minoritas.

5) Modelling
- mencari model svm yang cocok dengan dataset yang kita miliki.
  | Metode               | Kernel             | Akurasi  | Avg F1-score | Catatan                                                  |
| -------------------- | ------------------ | -------- | ------------ | -------------------------------------------------------- |
| Anxiety              | RBF (C=10, γ=0.01) | **0.97** | **0.9384**   | **Tertinggi**, tanpa SMOTE dan tanpa normalisasi         |
| Anxiety\_Smote       | RBF (default)      | 0.90     | 0.9248       | SMOTE meningkatkan keseimbangan, tapi menurunkan akurasi |
| Anxiety\_Norm        | Linear (C=10)      | 0.93     | 0.9107       | Normalisasi membantu, tapi masih di bawah model awal     |
| Anxiety\_Smote\_Norm | Linear (C=10)      | 0.93     | 0.9107       | Mirip dengan versi normalized                            |

- Berikut beberapa model yang saya coba dari model anxiety biasa dan mencoba jika dinormalisasikan dan juga SMOTE dari sini kita dapat membandingkannya.
- Afwan ustadz izin menanyakan sekiranya model mana yang lebih baik digunakan

6) Visualisasi
- Dikarenakan saya memiliki fitur Gender dan juga Prodi maka saya memandingkan antara gender dan prodi
> Mana yang lebih cenderung memiliki tingkat Kecemasan tinggi antara perempuan atau laki-laki
> Kemudia saya juga membandingkan atar prodi, dikarenkan setiap prodi tidak memiliki responden yang sama rata maka saya buatkan nilai rata-rata agar data tidak bias dan untuk mengetahui prodi mana yang cenderung memiliki tingkat kecemasan yang tinggi dan dikarenbakan ada prodi yang memiliki 1 responden maka saya filter dengan menampilkan rata2 prodi yang minimal punya 5 responden supaya tidak bias ke ouliner.
>  Saya juga ingin menampilkan fitur mana yang berpengaruh terhadap labelnya, dikarenakan SVM tidak punya output importance bawaan, saya menggunakan ANOVA F-test (Filter Method) untuk menganalisis aspek mana yang paling signifikan memengaruhi label. Jadi, saya menggabungkan insight visual distribusi per aspek dengan pengujian signifikan F-statistic.
  - Menggunakan SelectKBest(score_func=f_classif) ➜ artinya:
    > Menghitung nilai F-statistic untuk setiap fitur/aspek.
    > Nilai F menunjukkan seberapa besar variasi mean skor aspek antar kelas target (Mild, Moderate, Severe).
  -Kalau rata-rata skor aspek sangat beda antar kelas Anxiety/Stress, maka F-score tinggi.
  -Jadi aspek itu paling berpengaruh membedakan kelas target.
  -Kalau F-score kecil ➜ aspek itu tidak punya kekuatan signifikan memisahkan kelas.

7) Evaluation: Cross Validation
- Saya menggunakan CV untuk membuktikan model stabil di data split berbeda
- F1-Score tinggi = model baik
- dikarenakan data yang saya gunakan masih sebanyak 146 responden yang terdiri dari 73 mahasiswa dan 73 mahasiswi jadi saya menggunakan k-fold 3 dikarenakan datanya sedikit. Mungkin setelah menambah data bisa diuji dengan menggunakan k-fold 5.
