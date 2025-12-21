# Project-Based Internship Rakamin - Kimia Farma

## 📃 Background Analysis
Kimia Farma bertujuan untuk mengevaluasi kinerja bisnis perusahaan pada periode 2020 hingga 2023. Evaluasi ini mencakup data transaksi, inventori, informasi kantor cabang, serta data produk yang dimiliki perusahaan. Berdasarkan cakupan data tersebut, dilakukan analisis untuk menghasilkan insight yang bermakna dalam mendukung evaluasi kinerja bisnis. Pada akhirnya, insight yang dihasilkan dari analisis ini diharapkan dapat mendukung pengambilan keputusan yang akurat dan berbasis data.
***
## 📊 Description of Dataset 
Dalam analisis evaluasi kinerja bisnis ini, menggunakan empat dataset sebagai pendukung proses analisis dalam memperoleh *insight* serta pengambilan keputusan, dengan rincian sebagai berikut.

➔ kf_final_transaction: Sales transaction records and customer ratings.

➔ kf_inventory: Product inventory data across branches.

➔ kf_kantor_cabang: Branch office data.

➔ kf_product: Product master data.
***
## 👩‍💻 Teknik Analisis
Teknik analisis yang digunakan mulai dari pembuatan *query* menggunakan GoogleBigQuery sehingga dapat melakukan *aggregate* data yang dibutuhkan untuk keperluan analisis. Setelah itu, dapat melakukan visualisasi dari hasil *query* menggunakan Google Looker Studio.
***
## 📝 Proses Analisis
### a. Proses *Aggregate* Data Dengan GoogleBigQuery
Teknik analisis yang digunakan adalah mulai dari pembuatan *project* di GoogleBigQuery dengan nama **"Rakamin KF Analytics"**, dengan dilanjutkan untuk membuat *dataset* dengan nama **"kimia_farma"**. Kemudian, dapat memasukkan tabel-tabel yang digunakan berdasarkan penjelasan pada bagian **"Description of Dataset"** sehingga menghasilkan tampilan sebagai berikut.

<p align="center">
  <img src="https://github.com/user-attachments/assets/748d39ff-37c8-4ed2-860b-ddf038262079" width="250"/>
</p>

Setelah berhasil membuat *project* hingga memasukkan tabel-tabel yang diperlukan dalam proses analisis, selanjutnya dapat melakukan agregasi pada tabel-tabel yang ada untuk keperluan analisis selanjutnya, yakni visualisasi data dalam bentuk *dashboard* yang dilakukan menggunakan Google Looker. Untuk *query* yang diperlukan dalam proses agregasi tabel adalah sebagai berikut.

<details>
<summary><strong>Click to expand: BigQuery SQL – Create Analytics Table</strong></summary>

```sql
CREATE OR REPLACE TABLE kimia_farma.kf_tabel_analisa AS
SELECT
  t.transaction_id,
  t.date,
  t.branch_id,
  kc.branch_name,
  kc.kota,
  kc.provinsi,
  kc.rating AS rating_cabang,
  t.customer_name,
  t.product_id,
  p.product_name,
  t.price AS actual_price,
  t.discount_percentage,

  -- Perhitungan Gross Laba
  CASE
    WHEN t.price <= 50000 THEN 0.10
    WHEN t.price > 50000 AND t.price <= 100000 THEN 0.15
    WHEN t.price > 100000 AND t.price <= 300000 THEN 0.20
    WHEN t.price > 300000 AND t.price <= 500000 THEN 0.25
    ELSE 0.30
  END AS persentase_gross_laba,

  -- Nett Sales
  t.price * (1 - t.discount_percentage) AS nett_sales,

  -- Nett Profit
  (t.price * (1 - t.discount_percentage)) *
  CASE
    WHEN t.price <= 50000 THEN 0.10
    WHEN t.price > 50000 AND t.price <= 100000 THEN 0.15
    WHEN t.price > 100000 AND t.price <= 300000 THEN 0.20
    WHEN t.price > 300000 AND t.price <= 500000 THEN 0.25
    ELSE 0.30
  END AS nett_profit,

  t.rating AS rating_transaksi
FROM kimia_farma.kf_final_transaction t
LEFT JOIN kimia_farma.kf_kantor_cabang kc
  ON t.branch_id = kc.branch_id
LEFT JOIN kimia_farma.kf_product p
  ON t.product_id = p.product_id;
```

</details>

Tabel agregasi yang dibuat meliputi informasi transaksi, kantor cabang, produk, hingga perhitungan-perhitungan yang diperlukan, seperti perhitungan persentase gross laba yang dibagi berdasarkan kondisi harga, perhitungan nett sales dengan melakukan operasi perkalian antara harga dengan persentase diskon sehingga mendapatkan harga nett, serta menghitung nett profit yang juga dibagi berdasarkan kondisi harga produk. Tabel agregasi kemudian digabung menggunakan fungsi **LEFT JOIN** dari tabel **kf_final_transaction**, **kf_kantor_cabang**, serta **kf_product**.

Hasil dari proses agregasi tabel adalah sebagai berikut.

<p align="center">
  <img width="1180" height="461" alt="image" src="https://github.com/user-attachments/assets/869ee21e-363a-4178-b04b-db8b1f3ccea9"/>
</p>

### b. Visualisasi Data Dengan Google Looker
Untuk melihat hasil visualisasi data yang telah diimpor pada BigQuery dapat [klik di sini](https://instagram.com/putridwids)
