# data-analysis-using-SQL
Fashion Market Analysis Using MySQL

# Terdapat 3 tabel pada database situs Fashion, yaitu:
items (id, name, gender, price, cost)
sales_records (id, user_id, item_id, purchased_at)
users (id, name, gender, age)
# Pada tabel-tabel tersebut terdapat:
30 item terdaftar
910 sales record pada Juli 2018
325 user terdaftar


# total pendapatan, laba dan persentase laba
```
SELECT SUM(items.price) AS "total_penjualan", items.price, 
  1.0 * SUM(items.price - items.cost) / SUM(items.price) * 100 AS "persentase_laba"
FROM sales_records
JOIN items
ON sales_records.item.id = items.id;
```

# 5 item teratas berdasarkan pendapatan tertinggi
```
SELECT items.name, COUNT( * ) AS "total_penjualan", items.price,
       items.price * COUNT( * ) AS "total_pemasukan"
FROM sales_records
JOIN items
ON sales_records.item_id = items.id
GROUP BY items.name, items.price
ORDER BY COUNT( * ) * items.price DESC
LIMIT 5;
```

# Laba dan Persentase Laba dari 5 item teratas berdasarkan pendapatan tertinggi
```
SELECT items.name, (items.price - items.cost) AS "laba",
  1.0 * SUM(items.price - items.cost) / SUM(items.price) * 100 AS "persentase_laba",
  COUNT( * ) * (items.price - items.cost) AS "total_laba"
FROM sales_records
JOIN items
ON sales_records.item_id = items.id
WHERE items.id IN (
  SELECT items.id
  FROM sales_records
  JOIN items
  ON sales_records.item_id = items.id
  GROUP BY items.id
  ORDER BY COUNT ( * ) * items.price DESC
  LIMIT 5
  )
GROUP BY items.name, laba, persentase_laba
ORDER BY COUNT ( * ) * items.price DESC;
```

# 5 item teratas berdasarkan  laba tertinggi
```
SELECT items.name, COUNT( * ) AS "jumlah_penjualan", items.price,
       items.price * COUNT( * ) AS "total_pendapatan"
FROM sales_records
JOIN items
ON sales_records.item_id = items.id  
GROUP BY items.name, items.price
ORDER BY total_pendapatan ASC
LIMIT 5;
```

# 5 item teratas berdasarkan laba terendah
```
SELECT items.name, (items.price - items.cost) AS "laba",
  1.0 * SUM(items.price - items.cost) / SUM(items.price) * 100 AS "persentase_laba",
  COUNT( * ) * (items.price - items.cost) AS "total_laba"
FROM sales_records
JOIN items
ON sales_records.item_id = items.id
GROUP BY items.name, laba, persentase_laba
ORDER BY total_laba ASC
LIMIT 5;
```

# Total laba dan persentase laba berdasarkan gender
```
SELECT items.gender, SUM(items.price - items.cost) AS "total_laba",
  1.0 * SUM(items.price - items.cost) / SUM(items.price) * 100 AS "persentase_laba"
FROM sales_records
JOIN items
ON sales_records.item_id = items.id
GROUP BY items.gender;
```

# Jumlah penguna aktif
```
SELECT COUNT(DISTINCT(sales_records.user_id)) AS "jumlah_pengguna_aktif",
  1.0 * 100 * COUNT(DISTINCT(sales_records.user_id)) / (
  SELECT COUNT( * )
  FROM users
  )
  AS "persentase_pengguna_aktif"
FROM sales_records
JOIN users
ON sales_records.user_id = users.id;
```
# Frekuensi rata-rata pembelian, pengeluaran rata_rata dan pengeluaran rata-rata per pembelian dari pengguna aktif
```
SELECT 1.0 * COUNT( * ) / COUNT(DISTINCT(sales_records.user_id)) AS "frek_mean_pembelian",
  1.0 * SUM(items.price) / COUNT(DISTINCT(sales_records.user_id)) AS "mean_pengeluaran",
  1.0 * (1.0 * SUM(items.price) / COUNT(DISTINCT(sales_records.user_id))) / 
  (1.0 * COUNT( * ) / COUNT(DISTINCT(sales_records.user_id))) AS mean_pengeluaran_per_pembelian
FROM sales_records
JOIN users
ON sales_records.item_id = items.id;  
```

# List users yang melakukan pembelian lebih dari rata-rata
```
SELECT users.name, COUNT(sales_records.user_id) AS "jumlah_pembelian"
FROM sales_records
JOIN users
ON sales_records.user_id = users.id
GROUP BY users.name
HAVING jumlah_pembelian > (
    SELECT 1.0 * COUNT( * ) / COUNT(DISTINCT(sales_records.user_id)) AS "frek_mean_pembelian"
    FROM sales_records
    JOIN users
    ON sales_records.user.id = users.id
    )
GROUP BY jumlah_pembelian DESC;
```
