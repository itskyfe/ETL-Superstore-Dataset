# Superstore Data Warehouse

Proyek ini membangun **Data Warehouse** dari dataset *Sample Superstore* menggunakan proses **ETL (Extract, Transform, Load)** dengan Python dan menghasilkan skema bintang (*star schema*) di MySQL.

Proyek ini cocok untuk kebutuhan:

* Business Intelligence
* Decision Support System (DSS)
* Dashboard analitik
* Analisis penjualan dan profit

---

## Struktur File

```text
project-folder/
├── Superstore Dataset(4).csv     # Dataset mentah
├── superstore_clean.csv          # Dataset hasil transformasi
├── ETL_Superstore.ipynb          # Notebook proses ETL
├── superstore_dw.sql             # Script SQL data warehouse
└── README.md                     # Dokumentasi proyek
```

---

## Deskripsi File

### 1) `Superstore Dataset(4).csv`

Dataset mentah berisi data transaksi penjualan retail.

**Informasi umum:**

* Jumlah data: 9.994 baris
* Jumlah atribut: 21 kolom

**Kolom utama:**

* Order ID
* Order Date
* Ship Date
* Customer ID
* Customer Name
* Segment
* Country
* City
* State
* Postal Code
* Region
* Product ID
* Product Name
* Category
* Sub-Category
* Sales
* Quantity
* Discount
* Profit

---

### 2) `superstore_clean.csv`

Dataset yang sudah melalui proses pembersihan dan transformasi.

**Informasi umum:**

* Jumlah data: 9.994 baris
* Jumlah atribut: 29 kolom

**Kolom tambahan hasil feature engineering:**

* `shipping_days` : lama pengiriman dalam hari
* `profit_ratio` : rasio keuntungan terhadap penjualan
* `revenue` : pendapatan setelah diskon
* `order_year` : tahun transaksi
* `order_month` : bulan transaksi
* `order_quarter` : kuartal transaksi
* `month_label` : nama bulan
* `is_profitable` : indikator transaksi untung/rugi

---

### 3) `ETL_Superstore.ipynb`

Notebook Python yang menjalankan seluruh alur ETL.

**Tahapan utama:**

#### Extract

* Instalasi library yang dibutuhkan
* Upload dan pembacaan dataset CSV
* Pemeriksaan awal struktur data

#### Transform

* Standarisasi nama kolom
* Konversi tipe data
* Pengecekan missing value dan duplikasi
* Pembersihan data teks
* Feature engineering
* Validasi hasil transformasi

#### Load

* Penyusunan tabel dimensi
* Penyusunan tabel fakta
* Pembuatan query `CREATE TABLE`
* Pembuatan query `INSERT INTO`
* Export hasil ke file SQL

---

### 4) `superstore_dw.sql`

Script SQL untuk membangun dan mengisi data warehouse di MySQL.

**Isi file:**

1. Membuat database `superstore_dw`
2. Membuat tabel dimensi
3. Membuat tabel fakta
4. Menambahkan primary key dan foreign key
5. Mengisi seluruh data hasil ETL

---

## Arsitektur Data Warehouse

Proyek ini menggunakan **star schema**.

```text
                 dim_date
                    |
                    |
 dim_customers --- fact_orders --- dim_products
                    |
                    |
               dim_location
```

---

## Struktur Tabel

### `dim_customers`

Menyimpan data pelanggan.

| Kolom            |
| ---------------- |
| customer_pk (PK) |
| customer_id      |
| customer_name    |
| segment          |

### `dim_products`

Menyimpan data produk.

| Kolom           |
| --------------- |
| product_pk (PK) |
| product_id      |
| product_name    |
| category        |
| sub_category    |

### `dim_location`

Menyimpan data lokasi.

| Kolom            |
| ---------------- |
| location_pk (PK) |
| postal_code      |
| city             |
| state            |
| region           |
| country          |

### `dim_date`

Menyimpan dimensi waktu.

| Kolom        |
| ------------ |
| date_pk (PK) |
| full_date    |
| year         |
| month        |
| quarter      |
| month_name   |
| day_of_week  |

### `fact_orders`

Menyimpan data transaksi utama.

| Kolom            |
| ---------------- |
| row_id (PK)      |
| order_id         |
| date_pk (FK)     |
| ship_date        |
| ship_mode        |
| customer_pk (FK) |
| product_pk (FK)  |
| location_pk (FK) |
| sales            |
| quantity         |
| discount         |
| profit           |
| shipping_days    |
| profit_ratio     |
| revenue          |
| is_profitable    |

---

## Proses ETL

### 1. Extract

Mengambil data dari file CSV mentah.

### 2. Transform

Membersihkan data, menyesuaikan format, dan membuat atribut baru untuk analisis.

### 3. Load

Memuat data ke dalam struktur star schema pada MySQL.

---

## Analisis yang Didukung

Data warehouse ini dapat digunakan untuk analisis:

* Total penjualan per tahun
* Profit per kategori produk
* Produk paling menguntungkan
* Wilayah dengan penjualan tertinggi
* Segment pelanggan terbaik
* Rata-rata waktu pengiriman
* Tingkat profitabilitas transaksi

---

## Cara Menjalankan Proyek

### 1. Jalankan Notebook ETL

Buka `ETL_Superstore.ipynb` di Google Colab atau Jupyter Notebook.

### 2. Generate File SQL

Notebook akan menghasilkan file `superstore_dw.sql`.

### 3. Import ke MySQL

```sql
SOURCE superstore_dw.sql;
```

### 4. Verifikasi Database

```sql
USE superstore;
SHOW TABLES;
```

---

## Contoh Query Analisis

### Total Sales per Year

```sql
SELECT d.year, SUM(f.sales) AS total_sales
FROM fact_orders f
JOIN dim_date d ON f.date_pk = d.date_pk
GROUP BY d.year
ORDER BY d.year;
```

### Top 10 Produk Berdasarkan Profit

```sql
SELECT p.product_name, SUM(f.profit) AS total_profit
FROM fact_orders f
JOIN dim_products p ON f.product_pk = p.product_pk
GROUP BY p.product_name
ORDER BY total_profit DESC
LIMIT 10;
```

### Sales per Region

```sql
SELECT l.region, SUM(f.sales) AS total_sales
FROM fact_orders f
JOIN dim_location l ON f.location_pk = l.location_pk
GROUP BY l.region;
```

---

## Teknologi yang Digunakan

* Python
* Pandas
* NumPy
* Jupyter Notebook / Google Colab
* MySQL
