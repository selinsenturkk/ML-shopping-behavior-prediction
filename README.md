# Customer Shopping Behavior Prediction

## 1. Proje Tanımı ve Amacı
Bu proje, makine öğrenmesi tekniklerini kullanarak müşteri verilerinden anlamlı tahminler yapmayı amaçlamaktadır. Projede **Regression (Regresyon)** ve **Classification (Sınıflandırma)** olmak üzere iki farklı yaklaşım denenmiştir.

**Temel Hedefler:**
1.  Müşterilerin yapacağı harcama miktarını (`Purchase Amount`) tahmin etmek.
2.  Müşterinin "Abone Olup Olmadığını" (`Subscription Status`) tahmin etmek.

Çalışma boyunca veriler analiz edilmiş, modeller kurulmuş ve sonuçlara göre strateji değişikliğine gidilmiştir.

---

## 2. Veri Seti ve Ön Hazırlık
Kullanılan veri seti: `shopping_behavior_updated.csv`   
Kaggle linki için [buraya](https://www.kaggle.com/datasets/ayeshasiddiqa123/customer-shopping-behavior-dataset) tıklayınız.  
Veri setinde müşterilerin yaşı, cinsiyeti, satın aldıkları ürün kategorileri, lokasyonları ve harcama alışkanlıkları gibi bilgiler yer almaktadır.
### Veri Setinden Örnekler
İlk 5 satır aşağıdadır:

| Age | Gender | Item Purchased | Category | Purchase Amount (USD) | Location | Size | Color | Season | Review Rating | Subscription Status | Shipping Type | Discount Applied | Promo Code Used | Previous Purchases | Payment Method | Frequency of Purchases |
|---:|:---|:---|:---|---:|:---|:---|:---|:---|---:|:---|:---|:---|:---|---:|:---|:---|
| 55 | Male | Blouse | Clothing | 53 | Kentucky | L | Gray | Winter | 3.1 | Yes | Express | Yes | Yes | 14 | Venmo | Fortnightly |
| 19 | Male | Sweater | Clothing | 64 | Maine | L | Maroon | Winter | 3.1 | Yes | Express | Yes | Yes | 2 | Cash | Fortnightly |
| 50 | Male | Jeans | Clothing | 73 | Massachusetts | S | Maroon | Spring | 3.1 | Yes | Free Shipping | Yes | Yes | 23 | Credit Card | Weekly |
| 21 | Male | Sandals | Footwear | 90 | Rhode Island | M | Maroon | Spring | 3.5 | Yes | Next Day Air | Yes | Yes | 49 | PayPal | Weekly |
| 45 | Male | Blouse | Clothing | 49 | Oregon | M | Turquoise | Spring | 2.7 | Yes | Free Shipping | Yes | Yes | 31 | PayPal | Annually |

### Yapılan İşlemler (Preprocessing):
Modelin daha doğru çalışabilmesi için veriyi şu adımlarla hazırladım:

* **Gereksiz Verilerin Temizlenmesi:** `Customer ID` sütunu, sadece müşterilere verilen rastgele bir numara olduğu ve tahmin gücü taşımadığı için veri setinden çıkarıldı.
* **Kategorik Dönüşüm (One-Hot Encoding):** Bilgisayarlar kelimelerden anlamadığı için metin içeren sütunları (Örn: `Gender`, `Category`, `Season`) sayısal değerlere (`0` ve `1`) çevirdim.
* **Veri Ayırma (Train-Test Split):** Veriyi %80 eğitim (train) ve %20 test olacak şekilde ikiye ayırdım. Böylece modelin görmediği veriler üzerindeki başarısını ölçebildim.
---

## 3. Harcama Miktarını Tahmin Etme (Regresyon)

Projenin ilk aşamasında, müşterilerin özelliklerine bakarak **"Ne kadar harcama yapacak?"** (`Purchase Amount`) sorusuna yanıt aradım.

**Kullanılan Modeller:**
1.  **Linear Regression:** Değişkenler arasında doğrusal bir ilişki olup olmadığını test etmek için temel model olarak seçildi.
2.  **Random Forest Regressor:** Doğrusal olmayan karmaşık ilişkileri yakalayabilmesi umuduyla kullanıldı.

## 4. Regresyon Sonuçları ve Değerlendirme

Her iki model de eğitildikten sonra elde edilen sonuçlar beklentinin altında kaldı.

| Model | R² Score | RMSE (Hata Payı) |
|-------|----------|------------------|
| Linear Regression | -0.028 | 23.98 |
| Random Forest | -0.032 | 24.04 |

![lineer Regresyon Grafigi](/images/img2.png) ![Random forest grafigi](/images/img3.png)

### Neden Başarısız Oldu?
Elde edilen **negatif R² skorları**, modellerin veri setindeki varyansı açıklayamadığını gösterdi. Yapılan analiz sonucunda şu gerçek ortaya çıktı:
> Müşterinin yaşı, cinsiyeti veya puanı ile harcama miktarı arasında anlamlı bir ilişki bulunmamaktadır.

Bu nedenle, veri seti **sayısal tahmin (Regresyon)** yapmak için uygun değildir. Bu noktada strateji değişikliğine gidilmiştir.

---

## 5. Strateji Değişikliği ve Sınıflandırma (Abonelik Tahmini)

Regresyon modellerinin başarısızlığı üzerine, problemin tanımını değiştirdim. Hedef değişkeni **`Subscription Status` (Abonelik Durumu)** olarak belirleyip projeyi bir **Sınıflandırma (Classification)** problemine dönüştürdüm.

**Yeni Hedef:** Müşterinin özelliklerine bakarak "Abone mi?" yoksa "Değil mi?" sorusuna yanıt bulmak.

**Kullanılan Modeller:**
1.  **Logistic Regression**
2.  **Random Forest Classifier**

---

## 6. Model Performansı ve Karşılaştırma

Sınıflandırma modelleri, regresyonun aksine oldukça başarılı sonuçlar verdi.

**Sonuçlar:**
* **Logistic Regression Doğruluğu:** %80.1
* **Random Forest Classifier Doğruluğu:** %82.0

**Logistic Regression** modeli ve en iyi performansı gösteren **Random Forest** modelinin detaylı analiz tablosu aşağıdadır:

![Sınıflandırma Raporu Tablosu](/images/img5.png)

### Random Forest Tablo Yorumu:
Tabloda dikkat çeken en önemli detay **Recall (Duyarlılık)** değeridir.
* **Recall: 0.91**
* **Anlamı:** Modelimiz, gerçekte abone olan müşterilerin **%91'ini** doğru tespit etmiştir. Yani model, potansiyel aboneleri gözden kaçırmama konusunda "uzmanlaşmıştır".
* **Precision (0.63):** Model bazen abone olmayanları da abone gibi tahmin etse de, pazarlama stratejisi açısından aboneyi kaçırmamak daha önceliklidir.

Modelin doğru/yanlış tahminlerini gösteren hata matrisi:

![confusion matrix](/images/img7.png)

Bu tablo bize modelin stratejisini net bir şekilde göstermektedir:

1.  **Doğru Pozitifler (Sağ Alt Köşe):** Model, gerçekte abone olan kişilerin çok büyük bir kısmını doğru tespit etmiştir.
2.  **Yanlış Negatifler (Sol Alt Köşe):** Modelin "Abone Değil" dediği ama aslında "Abone Olan" kişi sayısı çok düşüktür.
    * **İş Dünyası Anlamı:** Şirketimiz, potansiyel sadık müşterilerini (aboneleri) gözden kaçırmıyor.
3.  **Yanlış Pozitifler (Sağ Üst Köşe):** Model bazen abone olmayanları da abone gibi tahmin etmiştir (Precision düşüklüğünün sebebi).
    * Pazarlama açısından bakıldığında bir aboneyi sistemden kaçırmaktansa, abone olmayana yanlışlıkla promosyon göndermek "tercih edilebilir" bir risktir. Modelimiz bu güvenli yolu seçmiştir.
      
---

## 7. En Önemli Faktörler (Feature Importance)

Modelin başarısının arkasında hangi özelliklerin yattığını anlamak için **Feature Importance** (Öznitelik Önemi) analizi yaptım.

![Feature Importance Grafiği](/images/img10.png)

### Analiz Sonucu:
Grafikte görüldüğü üzere, bir müşterinin abone olup olmadığını belirleyen en kritik iki faktör:
1.  **Promo Code Used (Promosyon Kodu Kullanılmış mı?)**
2.  **Discount Applied (İndirim Uygulanmış mı?)**

Yaş, cinsiyet veya harcama miktarı gibi özelliklerin etkisi daha sınırlı kalırken, şirketin sunduğu indirim ve kampanyaların abonelik üzerinde güçlü bir etkisi olduğu kanıtlanmıştır.

---

## 8. Sonuç:

Bu proje, Veri Bilimi süreçlerinde **"Problemi Doğru Tanımlamanın"** ne kadar hayati olduğunu göstermiştir. Negatif $R^2$ skorundan **%82 doğruluk** oranına geçiş, veri biliminde başarının veriye doğru soruyu sormaktan geçtiğini göstermektedir.

1.  **Regresyon Deneyimi:** Veri setindeki özellikler, müşterinin **ne kadar harcayacağını** tahmin etmek için yeterli sinyali içermemektedir (Negatif $R^2$).
2.  **Sınıflandırma Başarısı:** Aynı veri seti, müşterinin **abone olup olmayacağını** tahmin etmek için ise oldukça elverişlidir (%82 Başarı).
3.  **İş Zekası:** Analizler sonucunda, müşteri sadakatini artırmanın yolunun "İndirim" ve "Promosyonlardan" geçtiği veriye dayalı olarak ispatlanmıştır.

