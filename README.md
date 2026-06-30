# End-to-End CRM Analytics & Predictive Demand Forecasting Pipeline

Bu proje; e-ticaret tabanlı müşteri ve işlem verilerini işleyerek kurumsal düzeyde CRM (Müşteri İlişkileri Yönetimi) analitiği ve yapay zeka destekli gelecek dönem talep tahmini gerçekleştiren uçtan uca (end-to-end) bir veri bilimi pipeline'ıdır. Proje kapsamında müşterilerin geçmiş sadakat ve değer metrikleri (RFM) çıkarılmış, zaman serisi dinamikleri korunarak gelecekteki satın alma davranışları makine öğrenmesi algoritmalarıyla modellenmiştir.

## 🚀 Geliştirici Bilgileri
* **Adı Soyadı:** Ali Çelebi
* **Rol:** Yapısal Veri Analisti & Yapay Zeka Stajyeri
* **LinkedIn:** [linkedin.com/in/alicelebi1198/](https://www.linkedin.com/in/alicelebi1198/)

---

## 💎 Temiz Kod (Clean Code) ve Veri Disiplini Prensipleri
Bir Yapay Zeka Stajyerinin sahip olması gereken en kritik mühendislik yetkinlikleri bu projenin mimari temelini oluşturmaktadır:
1. **Veri Tipi Güvenliği (Data Type Optimization):** `CustomerID` gibi kategorik/kimlik belirten sütunların float olarak kalması ve bellek israfına yol açması engellenmiş; veri tipleri önce güvenli bir şekilde tam sayıya (`int`), ardından model manipülasyonu için metne (`string/object`) dönüştürülmüştür.
2. **Sıfır Veri Sızıntısı (Anti-Data Leakage):** Zaman serisi içeren tahminleme süreçlerinde en sık yapılan hata olan "rastgele veri bölme" (random split) yerine, zaman serisinin doğasına uygun olarak **Zaman Tabanlı Bölümleme (Time-Based Split)** uygulanmıştır. Gelecekteki verilerin geçmişe sızması kesin olarak engellenmiştir.
3. **Modüler ve Açıklanabilir Yapı:** Kod blokları karmaşadan uzak, tek bir amaca hizmet eden fonksiyonel adımlarla yazılmış ve baştan sona detaylı Türkçe yorum satırları ile belgelenmiştir.

---

## 🛠️ Teknolojik Altyapı ve Kütüphaneler
Projenin geliştirilmesinde aşağıdaki modern veri bilimi kütüphaneleri kullanılmıştır:
* **Pandas & NumPy:** Gelişmiş veri manipülasyonu, zaman pencereleri oluşturma ve matris operasyonları.
* **Scikit-Learn:** Makine öğrenmesi modellerinin eğitimi, hiperparametre yönetimi ve metrik hesaplamaları.
* **Seaborn & Matplotlib:** Tahmin sonuçlarının istatistiksel grafiklere dökülmesi ve analitik raporlama.

---

## ⚙️ Pipeline İş Akışı ve Model Mimarisi

### Adım 1: CRM Veri Temizliği (Data Cleaning)
* CRM analitiğinin temelini müşteri kimlikleri oluşturduğundan, `CustomerID` sütunundaki tüm eksik (`NaN`) değerler temizlenmiştir.
* İptal, iade veya hatalı kayıtları temsil eden negatif ya da sıfıra eşit `Quantity` ve `UnitPrice` değerleri filtrelenmiştir.
* Her bir satırın toplam harcamasını gösteren `TotalAmount` (Miktar × Birim Fiyat) özelliği türetilmiştir.

### Adım 2: Özgün Özellik Mühendisliği (Feature Engineering & RFM)
Müşterilerin geçmiş satın alma alışkanlıklarını özetlemek adına şu temel metrikler hesaplanmıştır:
* **Recency (Yenilik):** Müşterinin son faturasından bu yana geçen gün sayısı.
* **Frequency (Sıklık):** Müşteriye ait benzersiz fatura sayısı.
* **Monetary (Parasal Değer):** Müşterinin bıraktığı toplam net ciro.

**Hedef Değişken (Target - `NextMonthQuantity`):** Zaman pencereleri (Rolling Windows) mantığıyla veri aylık bazda gruplanmış ve her müşteri ile ürün (`StockCode`) çiftinin bir sonraki ay yapacağı toplam satış miktarı `shift(-1)` ile hedef değişken haline getirilmiştir. Zaman serisi sınırında oluşan ve geleceği bilinmeyen (NaN) satırlar pipeline'dan başarıyla arındırılmış ve tam sayıya çevrilmiştir.

### Adım 3: Zaman Serisi Bölümlemesi (Time-Based Train/Test Split)
Modellerin gerçek dünya senaryolarında test edilebilmesi için veri sızıntısı önlenerek şu şekilde bölünmüştür:
* **Eğitim Seti (Train Set):** Zaman serisinin başlangıcından son 60 güne kadar olan tüm geçmiş veri.
* **Doğrulama Seti (Validation Set):** Son 60 gün ile son 30 gün arasındaki veri penceresi.
* **Test Seti (Test Set):** Veri setindeki en güncel son 30 günlük dönem.

### Adım 4: Regresyon Modellemesi & Topluluk Öğrenmesi (Ensemble Learning)
Tahmin kararlılığını en üst düzeye çıkarmak için iki güçlü topluluk algoritması entegre edilmiştir:
1. **Random Forest Regressor:** Bagging ekolünü temsil eden, aşırı öğrenmeye (overfitting) karşı dirençli ağaç tabanlı model.
2. **Gradient Boosting Regressor:** Boosting ekolünü temsil eden, hataları sıralı olarak optimize eden güçlü algoritma.

**Topluluk (Ensemble) Stratejisi:** İki bağımsız modelin ürettiği tahminlerin *aritmetik ortalaması* alınarak varyansı düşük, genel yönetilebilirliği ve tahmin gücü yüksek hibrid bir Ensemble tahmini üretilmiştir.

---

## 📊 Performans Değerlendirme ve Görselleştirme
Modellerin başarısı Test Seti üzerinde **R-squared ($R^2$ Score)** ve **Mean Absolute Error (MAE)** metrikleri ile doğrulanmaktadır. İş birimlerine aksiyon alınabilir ticari içgörüler sunmak amacıyla:
* Gelecek ay en yüksek satış hacmine ulaşacağı tahmin edilen **ilk 10 ürün**,
* Gelecek ay en yüksek ciro potansiyeline sahip **ilk 10 müşteri segmenti** Seaborn kütüphanesiyle görselleştirilerek analitik rapora dönüştürülmektedir.

EKRAN GÖRÜNTÜLERİ :
<img width="945" height="469" alt="image" src="https://github.com/user-attachments/assets/58cf4d42-001a-468c-9619-c6693495f0e7" />
<img width="945" height="469" alt="image" src="https://github.com/user-attachments/assets/9a124c38-9a98-4250-a53b-c1e60da53219" />
<img width="945" height="469" alt="image" src="https://github.com/user-attachments/assets/320ab51a-92dd-4e0b-b2f7-1e541a37874a" />
<img width="945" height="654" alt="image" src="https://github.com/user-attachments/assets/d188f0c1-1591-49f5-bd97-e5d17545c4de" />
<img width="945" height="238" alt="image" src="https://github.com/user-attachments/assets/22f11ded-e84e-486c-a6e1-c3c0fc671252" />
<img width="865" height="1178" alt="image" src="https://github.com/user-attachments/assets/526e47ce-9ffb-4bcb-9574-889b85a0c768" />


## 🏃‍♂️ Nasıl Çalıştırılır?

1. Projeyi yerelinize klonlayın:
```bash
git clone [https://github.com/kullanici_adi/repo_adi.git](https://github.com/kullanici_adi/repo_adi.git)
cd repo_adi
