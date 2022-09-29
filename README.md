# FInal-Project_Bank-Marketing-Targets

## Problem - Goals - Business matrics
- **Problem:** Jumlah nasabah bank yang telah mendaftar deposito berjangka pada Perusahaan Bank ABC masih rendah jika dibandingkan dengan total nasabah bank tersebut. Dari total 45.211 nasabah yang ada, hanya sekitar 11,7 % nasabah saja yang mendaftar deposito berjangka.
- **Goals:** Meningkatkan jumlah nasabah bank yang mendaftar deposito berjangka hingga sebesar 20% atau dapat dikatakan  1 dari 5 nasabah bank mendaftar deposito berjangka
- **Objective:** Melakukan klasifikasi pada customer bank tersebut, sehingga nasabah yang sekiranya tertarik untuk membuka deposito berjangka dapat diprioritaskan terlebih dahulu untuk dihubungi/ditawari deposito berjangka.
- **Business metrics (yang dapat diukur, untuk tahu berhasil atau tidak):**
  - Jumlah nasabah yang mendaftar deposito berjangka

## Exploratory Data Analysis

### Descriptive Statistics
<img src="/image-stage1/info.png" alt="Alt text"  width="220" height="300">

Kita dapat melihat bahwa:
- Dataframe memiliki total 45211 baris dan 17 kolom
- Setiap kolom sudah memiliki tipe data yang sesuai
- Dataframe sudah tidak memiliki null value pada setiap kolomnya
- Target klasifikasi adalah kolom y dengan tipe data object
- Sisanya adalah feature (predictor)

### Duplicate Value
<img src="image-stage1/duplicate val.png" alt="Alt text"  width="220" height="300">

Dataset tidak memiliki duplicate value

### Statistical Summary
#### Numeric Columns
<img src="/image-stage1/num_describe.png" alt="Alt text"  width="220" height="300">

Beberapa pengamatan dari *statistical summary* kolom numerik di atas:
- Jika hanya melihat dari kedekatan nilai mean dan median serta posisi Q2 maka distribusi nilai di kolom `age`,`day`,`campaign` terlihat normal/simetrik (*mean* dan *median* cukup dekat, serta nilai Q2 kurang lebih berada di tengah2 Q1 - Q3).
- Sedangkan kolom `balance`,`duration`, tampaknya tidak simetrik, karena mean > median.
- Pada kolom `balance` terlihat bahwa nilai minimum memiliki angka negatif, yang dirasa wajar jika rekening customer tersebut merupakan rekening giro yang sedang menggunakan fasilitas kredit dari bank tersebut
- Pada kolom `pdays` nilai min, 25%, 50%, dan 75% berisikan -1, dimana angka tersebut merupakan suatu indikator bahwa 'client was not previously contacted in previous campaign'.

**Pembuktian bentuk distribusi kolom numerik**
Untuk membuktikan argumentasi mengenai bentuk distribusi setiap kolom berdasarkan statistical summary diatas akan dilakukan pengecekkan skewness score untuk kolom-kolom numerik tersebut:

<img src="/image-stage1/skewness.png" alt="Alt text"  width="220" height="300">

- Berarti untuk sementara bentuk persebaran data dapat dilihat bahwa ternyata hanya kolom `age` yang berdistribusi normal/simetrik selebihnya mengalami positif skewness. Namun untuk lebih pastinya lagi akan dilakukan dengan pengecekkan secara visualisasi bentuk distribusi setiap kolom saat melakukan Univariate Analysis

#### Categorical Columns
<img src="/image-stage1/cat_descirbe.png" alt="Alt text"  width="220" height="300">

Beberapa pengamatan dari *statistical summary* kolom kategorikal di atas:
- Kolom `job` dan `month`  memiliki jumlah nilai unique yang sangat banyak yaitu mencapai 12 nilai unique. Ada kemungkinan feature ini tidak akan terlalu berguna sebagai predictor.
- Mayoritas data customer memilih "no" untuk membuka deposito (dengan frekuensi 39922 dari 45211 data yang ada). Dimana artinya data customer yang memilih untuk membuka deposito hanya sekitar 11,7% dari total data. Hal ini tentunya menjadi suatu permasalahan dikarenakan dapat membuat model menjadi overfit. Harus dilakukan oversampling.

### Univariate Analysis
#### Outliers
<img src="/image-stage1/outlier.png" alt="Alt text"  width="220" height="300">

#### Distribution Plot
<img src="/image-stage1/distplot.png" alt="Alt text"  width="220" height="300">

    Mengenai bentuk distribusi dapat dipastikan bahwa:
- Untuk kolom `balance`, `duration`, `campaign`, `pdays` dan `previous` memiliki distribusi yang terlihat *skewed positif*
- Kolom `day` memiliki distribusi *multimodal*
- Kolom `age` memiliki distribusi yang hampir mendekati normal/simetrik

    Selain itu dapat dilihat juga bahwa:
- Distribusi kolom `age` terkonsentrasi pada umur dibawah 40 tahun
- Dari boxplot dapat dilihat bahwa outlier terlihat utamanya pada kolom `age`, `balance`, `duration`, `campaign`, `pdays` dan `previous`, Pada tahap preprocessing nanti akan dilakukan pengambilan keputusan dalam menghadapi outlier-outlier tersebut.
- Untuk features yg sebagian besar datanya merupakan outlier jika seandainya pada tahap preprocessing diputuskan untuk melakukan pembersihan terhadap outlier tersebut,sebaiknya dilakukan dengan menetapkan sebuah treshold. Misalkan dengan menetapkan treshold yaitu percentile 95% atau 99%. Sehingga dengan demikian data yang terbuang akibat preprocessing outlier tidak terlalu banyak.

#### Categorical Distribution
<img src="/image-stage1/cat distribution.png" alt="Alt text"  width="220" height="300">

- Untuk feature `job` meskipun memiliki nilai unique yang banyak dapat dilihat bahwa didominasi hanya oleh 5 jenis pekerjaan yaitu "blue-collar", "management", "technician", "admin", dan "services". Pada saat tahap preprocessing selain dari 5 jenis pekerjaan tersebut dapat dijadikan satu kelompok menjadi jenis "others".
- Seperti pengamatan kita sebelumnya, distribusi kolom target yaitu kolom `y` memiliki 2 value yaitu "yes" dan "no". Namun frekuensi antara value "yes" dan "no" tidak seimbang, dimana value "yes" hanya berkisar 11,7% dari keseluruhan value. Oleh sebab itu nanti harus dilakukan **Oversampling**.
- Untuk kolom `job`,`education`,`contact` yang memiliki nilai unknown, jika dirasa perlu kemungkinan pada saat preprocessing bisa digantikan dengan nilai mode nya
- Saat proses preprocessing kolom2 kategorikal ini akan dilakukan encoding.


### Multivariate Analysis
#### Monthly Yes
<img src="/image-stage1/yes ratio.png" alt="Alt text"  width="220" height="300">

* Dapat dilihat bahwa pada bulan may persentase customer yang melakukan pendaftaran deposito sedikit lebih tinggi dibandingkan bulan-bulan lainnya. Namun jika kita melihat secara keseluruhan pada setiap bulannya jumlah customer yang mendaftar tidaklah berbeda jauh antara satu bulan ke bulan lainnya, bahkan semua bulan tidak ada yg memiliki persentase "yes" lebih besar dari 0,2%. Hal ini dapat menjadi landasan jika kita memutuskan untuk tidak menggunakan feature `month` terhadap model yang akan kita dibuat, dikarenakan feature `month` tidak memiliki pengaruh yang signifikan perihal seorang customer tertarik mendaftar deposito berjangka.

#### Previous
<img src="/image-stage1/previous.png" alt="Alt text"  width="220" height="300">

* Dapat dilihat bahwa sebanyak lebih dari 35000 nasabah (tepatnya 36954) memiliki previous 0, dimana hal tersebut berarti nasabah belum pernah dihubungi pada campaign sebelumnya. Artinya sekitar 81% nasabah belum pernah dihubungi pada campaign sebelumnya. Dengan hal ini menunjukkan bahwa fitur `previous` tidak berpengaruh terhadap outcome y dikarenakan mayoritas dari customer memang belum pernah dihubungi pada campaign sebelumnya. Oleh sebab itu hal ini dapat dijadikan sebagai landasan kita apabila ingin memutuskan untuk tidak menggunakan feature `previous` terhadap model yang akan kita dibuat.


#### Correlation
<img src="/image-stage1/heatmap.png" alt="Alt text"  width="220" height="300">

* Tidak ada features numerical yang berkorelasi kuat dan dapat dilihat juga bahwa pada features numerical tidak ada yang redundan
* Nantinya setelah melakukan encoding pada tahap preprocessing akan juga dilakukan multivariate analysis untuk mengetahui korelasi antara semua features (baik yang kategorikal maupun numerik) terhadap kolom target yaitu kolom `y`


### Business Insight
#### Education
<img src="/image-stage1/bi_edu.png" alt="Alt text"  width="220" height="300">

* Dapat dilihat bahwa tingkat edukasi ternyata juga berpengaruh dalam keputusan seorang customer memilih untuk mendaftar deposito berjangka. Dari visualisasi diatas dapat dilihat bahwa kelompok education secondary dan tertiary merupakan kelompok education yang paling banyak memilih untuk mendaftar deposito berjangka, namun terlihat sangat jauh perbedaannya dengan kelompok education primary. Kiranya pihak bank bisa mulai melakukan kerjasama dengan sektor pendidikan untuk melakukan edukasi kepada anak-anak muda dengan cara memberikan seminar-seminar mengenai pentingnya berinvestasi (dalam konteks ini contohnya adalah deposito). Dengan demikian kesadaran dan keinginan untuk berinvestasi pun akan mulai tumbuh kepada generasi muda sehingga secara tidak langsung akan berpeluang meningkatkan jumlah orang yang akan membuka deposito dari segala tingkat pendidikan.

#### Contact
<img src="/image-stage1/bi_contact.png" alt="Alt text"  width="220" height="300">

* Bisa dilihat melalui visualisasi diatas bahwa tipe contact cellular merupakan tipe contact yang paling banyak menghasilkan customer untuk mendaftar deposito berjangka. Hal ini bisa terjadi karena di era sekarang ini orang-orang lebih banyak melakukan komunikasi melalui telepon celullar dibandingkan telepon biasa, dan hampir setiap orang pasti mempunyai telepon cellular. Dari sini pihak bank dapat mulai merubah strategi campaign untuk memprioritaskan melakukan contact melalui telepon celullar. Selain itu pihak bank sebelum melakukan panggilan secara langsung ke nomor telepon celullar, bisa juga bisa melakukan campaign melalui email atau melalui whatsapp sebagai tahapan awal dalam menawarkan deposito berjangka. Dengan demikian peluang customer yang mendaftar deposito berjangka akan meningkat.

#### Marital
<img src="/image-stage1/bi_marital.png" alt="Alt text"  width="220" height="300">


* Hasil visualisasi diatas dapat dilihat bahwa customer yang berstatus menikah dan single yang memiliki persentase paling besar untuk mendaftar deposito berjangka. Oleh sebab itu pihak bank bisa mulai fokus untuk memprioritaskan customer yang berstatus menikah dan single. Selain itu untuk meningkatkan kedua kelompok tersebut dalam mendaftar deposito berjangka pihak bank saat melakukan campaign kepada masing-masing individu dua kelompok tersebut dapat melakukan promosi dengan strategi pendekatan yang berbeda. Misalkan kepada kelompok "single" bisa melakukan promosi seperti "dengan bunga deposito dalam setahun adalah x% maka kira-kira dalam y tahun dana menikah akan dapat terkumpul". Sedangkan untuk kelompok "married" bisa melakukan promosi seperti "dengan bunga deposito dalam setahun adalah x% maka dana pendidikan untuk anak/dana pensiun saat masa tua akan terjamin"




