### 📘 RFM Analizi Nedir?

RFM (Recency, Frequency, Monetary) analizi, müşteri davranışlarını incelemek ve onları segmentlere ayırmak için kullanılan bir pazarlama analiz yöntemidir.
Bu yöntem üç temel metriğe dayanır:

Recency (Yakınlık): Müşteri en son ne zaman alışveriş yaptı?

Frequency (Sıklık): Müşteri ne sıklıkla alışveriş yapıyor?

Monetary (Parasal Değer): Müşteri toplamda ne kadar harcama yaptı?

Bu üç metriğe 1 ile 5 arasında skorlar verilerek her müşteri için bir RFM skoru hesaplanır.
Elde edilen skorlar, müşterileri şu segmentlere ayırmak için kullanılır:

💎 Sadık ve Kârlı Müşteriler

🌱 Yeni Müşteriler

⚠️ Riskteki Müşteriler

😴 Uyuyan Müşteriler

⚖️ Orta Segment Müşteriler

RFM analizi sayesinde işletmeler, müşteri sadakatini artırmak, kaybedilen müşterileri geri kazanmak ve hedefli kampanyalar oluşturmak için veriye dayalı stratejiler geliştirebilir.








### 📊 Görmek İstediğimiz Tablo

| Musteri_ID | Recency_Score | Frequency_Score | Monetary_Score | RFM_Score | Segment |
|------------|-------------  |-----------------|----------------|---------- |---------|
|            |               |                 |                |           |         |           
|            |               |                 |                |           |         |
|            |               |                 |                |           |         |
|            |               |                 |                |           |         |
| ...        | ...           | ...             | ...            | ...       | ...     |


### 📋 RFM Tablosu Yapısı

| **Sütun Adı**      | **Veri Türü**   | **Açıklama**                            |
|--------------------|----------------|------------------------------------------|
| Musteri_ID         | VARCHAR(5)     | Müşteri kimliği                          |
| Son_Siparis        | DATE           | Müşterinin son sipariş tarihi            |
| Recency            | INT            | Son siparişin üzerinden geçen gün sayısı |
| Frequency          | INT            | Toplam sipariş sayısı                    |
| Monetary           | INT            | Toplam harcama tutarı                    |

### 1-) 💻 RFM Tablosunu Oluşturalım

(Skorları en son toplu şekilde oluşturacağız)
```sql
CREATE TABLE Skor (
    Musteri_ID VARCHAR(5),
    Son_Siparis DATE,
    Recency INT,
    Frequency INT,
    Monetary INT
);
```
### 📋 Tablomuz

| Musteri_ID | Son_Siparis | Recency | Frequency | Monetary | 
|------------|-------------|---------|-----------|----------|
|            |             |         |           |          |
|            |             |         |           |          |  
|            |             |         |           |          |
|            |             |         |           |          | 
| ...        | ...         | ...     | ...       | ...      | 


### 2-) Tablomuza Müşteri ID Değerlerini Ekleyelim

```sql
insert into Skor (Musteri_ID)
SELECT DISTINCT [Customer ID] FROM ONLINERETAIL_2010
```

### 📋 Beklenen Tablo Çıktısı
(Değerler rastgele verilmiştir)

| Musteri_ID | Son_Siparis | Recency | Frequency | Monetary | 
|------------|-------------|---------|-----------|----------|
|   12456    |             |         |           |          |
|   12547    |             |         |           |          |  
|   14978    |             |         |           |          |
|   23145    |             |         |           |          | 
| ...        | ...         | ...     | ...       | ...      | 



### 3-) 💻 Skor Tablosuna Son Sipariş Tarihlerini Ekleyelim 


```sql
UPDATE Skor SET Son_Siparis=(SELECT MAX(InvoiceDate) 
FROM ONLINERETAIL_2010 where [Customer ID]=Skor.Musteri_ID)
```

### 📋 Beklenen Tablo Çıktısı
(Değerler rastgele verilmiştir)

| Musteri_ID | Son_Siparis | Recency | Frequency | Monetary | 
|------------|-------------|---------|-----------|----------|
|   12456    |  2011-10-11 |         |           |          |
|   12547    |  2010-05-01 |         |           |          |  
|   14978    |  2010-11-20 |         |           |          |
|   23145    |  2011-08-02 |         |           |          | 
| ...        | ...         | ...     | ...       | ...      | 

### 4-)💻 Skor Tablosunda Recency Değerlerini Hesaplayalım 
(Verisetindeki Son Tarihe Göre Hesapladık )

```sql
update Skor Set Recency= DATEDIFF(Day,Son_Siparis,'2011-12-31')
```

### 📋 Beklenen Tablo Çıktısı
(Değerler rastgele verilmiştir)

| Musteri_ID | Son_Siparis | Recency | Frequency | Monetary | 
|------------|-------------|---------|-----------|----------|
|   12456    |  2011-10-11 |   158   |           |          |
|   12547    |  2010-05-01 |   14    |           |          |  
|   14978    |  2010-11-20 |   101   |           |          |
|   23145    |  2011-08-02 |   45    |           |          | 
| ...        | ...         | ...     | ...       | ...      | 


### 5-)💻 Skor Tablosunda Frequency Değerlerini Hesaplayalım 
(Sipariş Sayılarını Yazdırdık)

```sql
UPDATE RFM SET Frequency=(SELECT COUNT(Distinct Invoice) FROM ONLINERETAIL_2010 where CustomerID=RFM.CustomerID)


```

### 📋 Beklenen Tablo Çıktısı
(Değerler rastgele verilmiştir)

| Musteri_ID | Son_Siparis | Recency | Frequency | Monetary | 
|------------|-------------|---------|-----------|----------|
|   12456    |  2011-10-11 |   158   |    15     |          |
|   12547    |  2010-05-01 |   14    |    147    |          |  
|   14978    |  2010-11-20 |   101   |    5      |          |
|   23145    |  2011-08-02 |   45    |    150    |          | 
| ...        | ...         | ...     | ...       | ...      | 



### 6-)💻 Skor Tablosunda Monetary Değerlerini Hesaplayalım 
(Toplam Sipariş Tutarını Hesapladık)
```sql

UPDATE RFM SET Monatery=(SELECT sum(Price*Quantity)  FROM ONLINERETAIL_2010 where CustomerID=RFM.CustomerID)

```
### 📋 Beklenen Tablo Çıktısı
(Değerler rastgele verilmiştir)


| Musteri_ID | Son_Siparis | Recency | Frequency | Monetary | 
|------------|-------------|---------|-----------|----------|
|   12456    |  2011-10-11 |   158   |    15     |   1850   |
|   12547    |  2010-05-01 |   14    |    147    |   7540   |  
|   14978    |  2010-11-20 |   101   |    5      |   2500   |
|   23145    |  2011-08-02 |   45    |    150    |   500    | 
| ...        | ...         | ...     | ...       | ...      | 



### 7-)💻 Alt tablo (CTE) ile RFM skorlarını hesaplayıp segment atamalarını yapalım
```sql

with tablo_rank as (
	select 
	Musteri_ID,
	Son_Siparis,
	Recency,
	Frequency,
	Monetary,
	NTILE(5) OVER( ORDER BY Recency desc) as Recency_Skor , --NTILE(5) ile değerlere 5 ile 1 arasında skor veriyoruz
	NTILE(5) OVER( ORDER BY Frequency desc) as Frequency_Skor,
	NTILE(5) OVER( ORDER BY Monetary desc) as Monetary_Skor



from Skor ),

son_tablo AS (
	SELECT 
		Musteri_ID,
		Recency_Skor,
		Frequency_Skor,
		Monetary_Skor,
		Recency_Skor + Frequency_Skor + Monetary_Skor AS RFM_Skoru
	FROM tablo_rank
)
SELECT 
    Musteri_ID,
    Recency_Skor,
    Frequency_Skor,
    Monetary_Skor,
    RFM_Skoru,
    CASE
        WHEN Recency_Skor >= 4 AND Frequency_Skor >= 4 AND Monetary_Skor >= 4 THEN '💎 Sadık ve Kârlı Müşteri'
        WHEN Recency_Skor <= 2 AND Frequency_Skor <= 2 THEN '😴 Uyuyan Müşteri'
        WHEN Recency_Skor >= 4 AND Frequency_Skor <= 2 THEN '🆕 Yeni Müşteri'
        WHEN Recency_Skor <= 2 AND Frequency_Skor >= 4 THEN '⚠️ Riskteki Müşteri'
        ELSE '⭐ Orta Segment Müşteri'
    END AS Segment
FROM son_tablo order by RFM_Skoru

```


