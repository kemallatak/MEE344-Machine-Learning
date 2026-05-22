# MEE344 Makine Öğrenmesi Projesi - Jüri Savunması Sunum Taslağı

Bu taslak, MEE344 Makine Öğrenmesi dersi jüri değerlendirmesinde sunulmak üzere, 4. sınıf mühendislik seviyesine yakışır bir akademik ve profesyonel üslupla hazırlanmıştır. Sunumun tahmini süresi 12-15 dakikadır.

---

### Slayt 1: Kapak Slaytı (Title & Introduction)
* **Slayt Başlığı:** Makine Öğrenmesi ile Türkiye Elektrik Şebekesi için Saatlik Yük Tahmini ve Saatlik Yük Tahmin Modeli
* **Slayt Alt Başlığı:** MEE344 Makine Öğrenmesi Dönem Projesi Savunması
* **Slayt İçeriği:**
  * Proje Danışmanı: [Hocanızın Unvanı & Adı]
  * Geliştiren: [Adınız Soyadınız] - Mühendislik Fakültesi, Makine/Elektrik-Elektronik Mühendisliği Bölümü
  * Tarih: 22 Mayıs 2026
  * Kurum: Ticaret Üniversitesi (veya Üniversiteniz)
* **Konuşma Notları:**
  > *"Değerli jüri üyeleri, hocalarım ve arkadaşlarım, MEE344 Makine Öğrenmesi dersi kapsamında hazırladığım 'Makine Öğrenmesi ile Türkiye Elektrik Şebekesi için Saatlik Yük Tahmini' projesinin jüri savunmasına hoş geldiniz. Bu sunumda, elektrik şebekemizin dinamiklerini ve tüketim desenlerini makine öğrenmesi modeli geliştirerek nasıl modellediğimi, tasarladığım üst düzey özellik mühendisliği (feature engineering) yaklaşımlarını ve elde ettiğim yüksek doğruluktaki sonuçları paylaşacağım."*

---

### Slayt 2: Problem Tanımı ve Araştırma Soruları (Problem Definition)
* **Slayt Başlığı:** Problem Tanımı ve Araştırma Hedefleri
* **Slayt İçeriği:**
  * **Problemin Türü:** Regresyon (Regression). Sürekli (continuous) saatlik elektrik yükünün (`Toplam`) tahmini.
  * **Hedef Değişken:** Saatlik Şebeke Yükü ($Toplam_t \in \mathbb{R}^+$) - MWh cinsinden.
  * **Ana Hedef:** Tarih, zaman ve geçmiş tüketim alışkanlıklarından yola çıkarak $t$ anındaki güç talebini en düşük hata ile tahmin etmek.
  * **Bilimsel Sınırlandırma (Leakage Prevention):** Şebeke üretim kaynaklarının (Doğal Gaz, Barajlı, vb.) toplamı doğrudan hedef değişkene eşit olduğu için bu değişkenler model girdisi olarak kullanılmamıştır. Model yalnızca şebeke zaman serisinin kendi desenlerine dayanmaktadır.
* **Konuşma Notları:**
  > *"Projemizin temel amacı, elektrik şebekesindeki saatlik toplam güç talebini tahmin etmektir. Tahmin etmeye çalıştığımız hedef değişken, MWh biriminde sürekli bir sayı olduğu için bu bir regresyon problemidir. Burada kritik bir mühendislik yaklaşımının altını çizmek istiyorum: Veri setimizde Doğal Gaz, Rüzgar, Kömür gibi münferit üretim kaynakları da bulunmaktaydı. Matematiksel olarak bu kaynakların toplamı o saatteki toplam talebe eşittir. Modellemede bu kaynakları girdi olarak kullanmak yapay bir %100 doğruluk verir ki bu veri sızıntısıdır (data leakage) ve pratik değeri yoktur. Gerçekçi bir load forecasting modeli kurabilmek için şebekenin tarih, saat ve kendi geçmiş tüketim davranışlarından başka hiçbir veriyi girdi olarak kabul etmedik."*

---

### Slayt 3: Şebeke Tahmininin Önemi ve Motivasyon (Motivation & Sectoral Context)
* **Slayt Başlığı:** Neden Saatlik Yük Tahmini? (Sektörel Motivasyon)
* **Slayt İçeriği:**
  * **Şebeke Kararlılığı:** Üretim ve tüketim anlık olarak dengelenmelidir; aksi halde frekans dalgalanmaları ve black-out (sistem çökmesi) yaşanır.
  * **Ekonomik Optimizasyon:** Gün Öncesi Piyasası (GÖP) ve Dengeleme Güç Piyasası'nda (DGP) doğru tahmin, milyonlarca liralık dengesizlik maliyetlerini önler.
  * **Yenilenebilir Enerji Entegrasyonu:** Rüzgar ve Güneş gibi kesintili kaynakların sisteme entegrasyonu, baz yük santrallerinin doğru planlanması için yük tahminini zorunlu kılar.
  * **Bakım Planlaması:** Şebeke operatörlerinin (TEİAŞ vb.) plansız kesintileri önlemesi için talep vadilerini belirlemesini sağlar.
* **Konuşma Notları:**
  > *"Elektrik enerjisi, doğası gereği üretildiği anda tüketilmek zorundadır; depolanması son derece maliyetlidir. Üretim ve talep dengelenemezse şebeke frekansı bozulur ve büyük çökmeler yaşanır. Sektörel olarak, yük tahmini yapan algoritmalar Gün Öncesi Piyasası'nda elektrik fiyatlarının belirlenmesi ve dengesizlik cezalarının önlenmesi açısından hayati önem taşır. Ayrıca, rüzgar ve güneş gibi kesintili temiz kaynaklar şebekede arttıkça, arka plandaki termik veya hidrolik santrallerin üretim planlamasını yapabilmek ancak doğru bir yük tahminiyle mümkündür."*

---

### Slayt 4: Veri Seti Genel Bakışı ve Üretim Kompozisyonu (Dataset Overview)
* **Slayt Başlığı:** Veri Seti ve Türkiye Elektrik Üretim Kompozisyonu
* **Slayt İçeriği:**
  * **Veri Kaynağı:** Türkiye Elektrik Şebekesi Saatlik Üretim Verileri (Mart - Haziran 2025).
  * **Gözlem Sayısı:** 2,232 saatlik veri (Noktasız, tam zaman serisi).
  * **Ortalama Saatlik Yük:** 33,634 MWh (Min: 15,890 MWh - Maks: 46,784 MWh).
  * **Kaynak Dağılımı (Ana Paydaşlar - Doughnut Analizi):**
    * *İthal Kömür:* %18.85
    * *Doğal Gaz:* %18.31
    * *Barajlı Hidrolik:* %16.46
    * *Linyit:* %12.25
    * *Rüzgar:* %12.20
    * *Akarsu (Run-of-river):* %10.48
    * *Diğer (Güneş, Biyokütle, Jeotermal vb.):* %11.45
* **Konuşma Notları:**
  > *"Çalışmamızda kullandığımız veri seti, Mart ile Haziran 2025 dönemine ait 2,232 saatlik gerçek Türkiye elektrik şebekesi verilerini kapsamaktadır. Ortalama saatlik talebimiz yaklaşık 33.6 bin Megavat-saattir. Slayttaki dairesel grafiğimizden de görebileceğiniz üzere, Türkiye'nin elektrik üretim kompozisyonunda en büyük payı %18.85 ile İthal Kömür ve %18.31 ile Doğal Gaz almaktadır. Onları %16.46 ile Barajlı hidrolik santralleri takip etmektedir. Rüzgar ve Güneş gibi çevre dostu kesintili kaynaklar ise toplamda %15'e yakın bir paya sahiptir."*

---

### Slayt 5: Açıklayıcı Veri Analizi (EDA) - Tüketim Isı Haritası (EDA - Heatmap)
* **Slayt Başlığı:** EDA - Saatlik ve Haftalık Elektrik Tüketim Desenleri
* **Slayt İçeriği:**
  * **Günlük Döngü (Diurnal Cycle):** Tüketim sabah saat 07:00 itibarıyla hızla tırmanmakta, 11:00-14:00 arasında tepe noktasına ulaşmakta ve gece 03:00'te en dip seviyesine gerilemektedir.
  * **Haftalık Profil:** Pazartesi - Cuma günleri arasında yüksek seviyede seyreden sanayi üretimi ve ticari faaliyetler, hafta sonu (Cumartesi ve özellikle Pazar günleri) yerini dramatik bir talep düşüşüne bırakmaktadır.
  * *Veri Görselleştirme:* `04_hourly_weekly_heatmap.png` ısı haritasında bu dinamikler net bir şekilde renk geçişleriyle gözlemlenmiştir.
* **Konuşma Notları:**
  > *"Veriyi ön işlemeden önce, şebekenin karakteristik yapısını anlamak için detaylı bir Açıklayıcı Veri Analizi gerçekleştirdim. Slaytta sunduğum saatlik ve haftalık ısı haritası şebekenin iki büyük döngüsünü ortaya koyuyor. Birincisi günlük döngü: Sabah saat 07:00'de mesainin başlamasıyla talep hızla yükseliyor ve öğle saatlerinde tepe yapıyor. Gece yarısından sonra ise dip noktaya ulaşıyor. İkincisi haftalık döngü: Pazartesi ile Cuma arasındaki yoğun sanayi ve ofis talebi, hafta sonu -özellikle Pazar günleri- yerini çok ciddi bir talep düşüşüne bırakıyor. Bu durum şebekemizin güçlü bir periyodik karaktere sahip olduğunu gösterir."*

---

### Slayt 6: Veri Ön İşleme Adımları (Data Preprocessing)
* **Slayt Başlığı:** Veri Ön İşleme ve Kararlılık Analizi
* **Slayt İçeriği:**
  * **Format ve Kodlama Giderimi:** Ondalık basamakların virgülle ayrıldığı, noktalı virgüllü Türkçe karakterli CSV yapısı CP1254 kodlamasıyla Pandas ortamına alınmıştır.
  * **Karakter Temizliği:** Sütun isimleri makine öğrenmesi standartlarına (ASCII yılan gösterimi - snake_case) getirilmiştir.
  * **Eksik Veri Analizi:** Veri setinde hiç eksik değer olmadığı doğrulanmıştır. Herhangi bir şebeke kesintisi riskine karşı modele ileri-geri yönlü dolgu (`ffill` ve `bfill`) mekanizması kurulmuştur.
  * **Zaman Dönüşümü:** Tarih ve Saat metinleri birleştirilerek, zaman indeksini korumak için `datetime64` veri tipine dönüştürülmüştür.
* **Konuşma Notları:**
  > *"Veri ön işleme adımında ilk olarak Türkçe karakter ve ondalık ayraç (virgül) gibi Python'ın standart olarak hata verebileceği format karmaşıklıklarını CP1254 kodlaması kullanarak Pandas üzerinde çözdüm. Sütun isimlerini tamamen ASCII karakterlere ve küçük/büyük harf standartlarına uygun hale getirdim. Veride eksik veri bulunmamaktadır ancak gerçek zamanlı sistemler için geliştirdiğimiz modele forward-fill ve backward-fill entegrasyonu yaparak eksik veri hassasiyetini sıfıra indirdik. Son olarak, tarih ve saat değişkenlerini tek bir datetime nesnesi altında birleştirerek zaman sıralamasını indeks olarak garanti altına aldık."*

---

### Slayt 7: Özellik Mühendisliği I - Zaman ve Döngüsel Kodlama (Feature Engineering I)
* **Slayt Başlığı:** Özellik Mühendisliği: Zaman Özellikleri ve Döngüsel Kodlama
* **Slayt İçeriği:**
  * **Zaman Öznitelikleri:** Günün Saati, Haftanın Günü, Ay, Yılın Günü, Hafta Sonu Bilgisi, Mevsimsellik.
  * **Döngüsel Kodlamanın Önemi:**
    * Saat 23 ile Saat 00 arasındaki yakınlık numerik olarak (23 - 0 = 23 birim uzaklık) kaybolur.
    * Çözüm olarak **Trigonometrik Dönüşüm (Sinüs/Kosinüs Kodlaması)** uygulanmıştır:
      $$Hour_{sin} = \sin\left(\frac{2\pi \cdot Hour}{24}\right), \quad Hour_{cos} = \cos\left(\frac{2\pi \cdot Hour}{24}\right)$$
  * **Fayda:** Doğrusal modellerin zamanın periyodik doğasını yakalaması sağlanmıştır.
* **Konuşma Notları:**
  > *"Model performansını artırmak için gerçekleştirdiğim Özellik Mühendisliği projenin en özgün kısmıdır. Slaytta döngüsel kodlamanın mantığını paylaşıyorum. Standart bir modelde saati 0'dan 23'e kadar bir sayı olarak verirseniz, model saat 23 ile gece 00'ın birbirine bitişik olduğunu anlayamaz, aksine aralarında 23 birimlik büyük bir mesafe olduğunu varsayar. Bu sorunu çözmek için saati 24 saatlik bir çember etrafına yerleştirerek Sinüs ve Kosinüs bileşenlerini hesapladım. Böylece saat 23 ve 00, trigonometrik olarak birbirine en yakın değerler haline geldi. Aynı döngüsel kodlamayı haftanın günleri ve aylar için de uyguladım."*

---

### Slayt 8: Özellik Mühendisliği II - Geçmiş Gecikmeler ve Volatilite (Feature Engineering II)
* **Slayt Başlığı:** Özellik Mühendisliği: Zaman Gecikmeleri (Lags) ve Hareketli İstatistikler
* **Slayt İçeriği:**
  * **Zaman Gecikmeleri (Lag Features):**
    * `Toplam_lag_1` (1 saat önceki tüketim - kısa dönem trend).
    * `Toplam_lag_2` (2 saat önceki tüketim).
    * `Toplam_lag_24` (Dün aynı saatteki tüketim - günlük periyot).
    * `Toplam_lag_168` (Geçen hafta aynı gün ve saatteki tüketim - haftalık periyot).
  * **Hareketli İstatistikler (Rolling Statistics):**
    * `Toplam_rolling_mean_6`: Son 6 saatin ortalama talebi.
    * `Toplam_rolling_mean_24`: Son 24 saatin ortalama talebi.
    * `Toplam_rolling_std_24`: Son 24 saatin tüketim dalgalanması (volatilite).
* **Konuşma Notları:**
  > *"Zaman serilerinde en güçlü tahmin edici verinin kendi geçmişidir. Elektrik yükü tahmininde 'oto-korelasyon' yani geçmişe bağımlılık çok güçlüdür. Bu doğrultuda modele 1 saat öncesinin, 2 saat öncesinin, dün aynı saatteki tüketimin (24 saatlik gecikme) ve tam bir hafta önceki aynı gün ve saatteki tüketimin (168 saatlik gecikme) verilerini ekledim. Ayrıca şebekenin kısa ve orta vadeli genel tüketim seyrini yakalaması için son 6 ve 24 saatlik hareketli ortalamaları ve şebekedeki anlık volatiliteyi temsil etmesi amacıyla son 24 saatin hareketli standart sapmasını (volatilite) hesaplayarak model girdisi olarak sundum."*

---

### Slayt 9: Değerlendirme Metodolojisi ve Kronolojik Doğrulama (Methodology)
* **Slayt Başlığı:** Değerlendirme Metodolojisi ve Kronolojik Doğrulama
* **Slayt İçeriği:**
  * **Veri Bölümleme (Chronological Train/Test Split):**
    * Rastgele bölme (Random Split) zaman serilerinde **Veri Sızıntısına (Look-ahead bias)** yol açar.
    * Çözüm olarak veri **kronolojik olarak %80 Eğitim (Train), %20 Test** olarak bölünmüştür.
    * *Eğitim Kümesi:* 8 Mart 2025 - 15 Mayıs 2025 (1,651 saat).
    * *Test Kümesi:* 15 Mayıs 2025 - 1 Haziran 2025 (413 saat).
  * **Çapraz Doğrulama Stratejisi:** Zaman serisi uyumlu 5-Fold **`TimeSeriesSplit`**.
* **Konuşma Notları:**
  > *"Makine öğrenmesinde en sık yapılan akademik hata, zaman serisi verilerinde rastgele train/test ayrımı yapılmasıdır. Gelecekteki bir saat eğitim kümesine, geçmişteki bir saat test kümesine düşerse model gelecekten kopya çeker (look-ahead bias) ve gerçek hayatta çalışmaz. Bu yüzden veriyi kronolojik olarak böldüm. İlk %80'lik dilimi (Mart ortasından Mayıs ortasına kadar) eğitim seti olarak, son %20'lik dilimi (Mayıs ortasından Haziran başına kadar) ise test seti olarak sakladım. Hiperparametre optimizasyonu yaparken de standart K-Fold yerine, zamanın kronolojik akışını bozmayan 5-fold TimeSeriesSplit çapraz doğrulamasını kullandım."*

---

### Slayt 10: Uygulanan Modeller ve Optimizasyon (Applied Models)
* **Slayt Başlığı:** Geliştirilen Modeller ve Hiperparametre Optimizasyonu
* **Slayt İçeriği:**
  * **Model 1: Doğrusal Regresyon (Linear Regression - Baseline)**
    * Girdiler standartlaştırılarak (`StandardScaler`) eğitilmiştir. Katsayı genlikleri (weights) analiz edilerek yordanabilir bir temel çizgi kurulmuştur.
  * **Model 2: XGBoost Regressor (Gelişmiş Ağaç Tabanlı Ensemble)**
    * Doğrusal olmayan ilişkileri, anlık sıçramaları ve değişken etkileşimlerini yakalamak için tercih edilmiştir.
  * **GridSearchCV Hiperparametre Optimizasyonu (XGBoost):**
    * `n_estimators` (Ağaç sayısı): [100, **200**]
    * `max_depth` (Maksimum ağaç derinliği): [3, 5, 7] -> **Best: 3**
    * `learning_rate` (Öğrenme oranı): [0.03, **0.1**]
    * `subsample` (Örnekleme oranı): [0.8, **1.0**]
* **Konuşma Notları:**
  > *"Projemizde iki farklı felsefede model eğittim. İlk modelimiz Doğrusal Regresyon. Doğrusal regresyon girdileri standardize edilmiş şekilde aldı ve bizim için son derece yorumlanabilir, katsayı analizi yapabildiğimiz bir baseline oluşturdu. İkinci ve ana modelimiz ise gradyan artırma algoritması olan XGBoost Regressor'dur. Karar ağacı tabanlı bu algoritmayı optimize etmek için TimeSeriesSplit ile çapraz doğrulamalı bir GridSearch gerçekleştirdim. En iyi hiperparametre kombinasyonu olarak: 200 ağaç sayısı (n_estimators), 3 maksimum derinlik (max_depth) ve 0.1 öğrenme oranı (learning_rate) elde edilmiştir. Ağaç derinliğinin 3 gibi sığ çıkması, modelin aşırı öğrenmeden (overfitting) kaçındığını göstermektedir."*

---

### Slayt 11: Deneysel Performans Sonuçları (Experimental Results)
* **Slayt Başlığı:** Performans Sonuçları ve Sistematik Karşılaştırma
* **Slayt İçeriği:**

| Model Türü | Veri Kümesi | $R^2$ (Açıklayıcılık Oranı) | RMSE (MWh) | MAE (MWh) |
| :--- | :--- | :---: | :---: | :---: |
| Doğrusal Regresyon | Eğitim (Train) | %96.78 | 1017.76 | 774.83 |
| Doğrusal Regresyon | **Test Kümesi** | **%97.13** | **925.07** | **711.24** |
| XGBoost Regressor | Eğitim (Train) | %99.26 | 488.68 | 372.44 |
| XGBoost Regressor | **Test Kümesi** | **%98.66** | **631.43** | **474.74** |

  * **R² Değerlendirmesi:** XGBoost, test kümesinde **%98.66** gibi son derece yüksek bir başarıya ulaşmıştır.
  * **Baseline Başarısı:** Doğrusal Regresyon modelinin test R² skoru **%97.13**'tür.
* **Konuşma Notları:**
  > *"Slaytta modellerimizin eğitim ve test kümelerindeki hata metriklerini karşılaştırmalı olarak sunuyorum. XGBoost modeli, daha önce görmediği test verisinde %98.66'lık bir R² skoru elde etmiştir. Yani şebeke yükündeki dalgalanmanın %98.66'sını başarıyla açıklamaktadır. Ortalama mutlak hatası ise sadece 474.7 MWh'tır. Bu, yaklaşık 33.6 bin MWh olan ortalama talebin sadece %1.4'üne denk gelen inanılmaz derecede küçük bir hata marjıdır. Diğer taraftan, doğrusal regresyon baseline modelimizin de test setinde %97.13 gibi fevkalade bir R² elde etmesi, uyguladığımız öznitelik mühendisliğinin gücünü kanıtlamaktadır."*

---

### Slayt 12: Tahmin Kalitesi ve Zaman Serisi Uyumu (Prediction Visualization)
* **Slayt Başlığı:** Tahmin Kalitesi ve Zaman Serisi Grafiği Uyumu
* **Slayt İçeriği:**
  * **Test Kümesinden Örnek Bir Hafta (168 Saat):** Gerçek Yük, Doğrusal Regresyon ve XGBoost tahminlerinin saatlik karşılaştırması.
  * **Önemli Bulgular:**
    * XGBoost (Mavi kesikli çizgi) gerçek yükü (Siyah düz çizgi) tepe ve dip noktalarda neredeyse birebir takip etmektedir.
    * Hafta sonuna geçişteki talep düşüşü ve gece dalgalanmaları model tarafından hatasız yakalanmıştır.
  * *Veri Görselleştirme:* `06_predictions_vs_actual.png` grafiğinde tahminlerin gerçek değerler üzerindeki örtüşme başarısı kanıtlanmıştır.
* **Konuşma Notları:**
  > *"tahmin kalitesini görsel olarak değerlendirmek adına, test setinin ilk bir haftasından yani 168 saatlik bir dilimden aldığım 'Gerçek vs Tahmin' grafiğini inceleyebiliriz. Siyah çizgi gerçek şebeke yükünü, mavi kesikli çizgi XGBoost tahminlerini, kırmızı kesikli çizgi ise Doğrusal Regresyonu gösteriyor. Grafik üzerinde de netçe görüldüğü üzere, XGBoost modeli şebekenin sabah piklerini, akşamüstü düşüşlerini ve hafta sonuna geçişteki ani talep düşüşünü neredeyse milimetrik bir doğrulukla yakalamaktadır. İki model de şebekenin dinamik yapısını kavramada son derece kararlıdır."*

---

### Slayt 13: Değişken Önem Dereceleri Analizi (Feature Importance)
* **Slayt Başlığı:** Öznitelik Önem Dereceleri (Feature Importance)
* **Slayt İçeriği:**
  * **En Etkili Özellikler (XGBoost & Linear):**
    * **`Toplam_lag_1` (1 Saat Önceki Yük):** En baskın tahminsel özniteliktir. Şebekenin kısa vadeli momentumunu temsil eder.
    * **`Toplam_lag_24` (Dün Aynı Saat):** Günlük tüketim döngüsünün (diurnal cycle) en kararlı tahmincisidir.
    * **`Hour_sin` / `Hour_cos`:** Gün içi pik ve vadi geçişlerini pürüzsüzleştirir.
  * **Analiz:** Elektrik yükü serisinde otokorelasyon ve takvim desenleri şebeke dinamiklerini yönlendiren ana unsurlardır.
* **Konuşma Notları:**
  > *"Modellerimizin kararlarını nasıl verdiğini açıklamak (Explainable AI) bir mühendislik projesi için elzemdir. Slayttaki grafik, iki modelimizin de öznitelik önem derecelerini gösteriyor. Beklendiği üzere, en etkili iki özellik Toplam_lag_1 yani bir önceki saatin tüketimi ve Toplam_lag_24 yani dünkü aynı saatteki tüketimdir. Bu durum elektrik şebekesindeki güçlü otokorelasyon yapısını doğrulamaktadır. Zaman tabanlı döngüsel sin/cos özelliklerimiz de modellerin gün içi geçişleri yumuşak bir şekilde öğrenmesinde ve saatlik ısı desenlerini kavramasında üçüncü en önemli faktör olmuştur."*

---

### Slayt 14: Sektörel Uygulama Alanları ve Limitasyonlar (Sectoral Impact & Limitations)
* **Slayt Başlığı:** Sektörel Uygulamalar, Limitasyonlar ve Gelecek Çalışmalar
* **Slayt İçeriği:**
  * **Sektörel Uygulama:**
    * Akıllı şebeke (Smart Grid) kontrol sistemlerine entegrasyon.
    * Enerji santrallerinin start-stop ve yük atma (load shedding) planlaması.
    * Gün Öncesi Piyasası'nda optimum teklif stratejileri oluşturulması.
  * **Limitasyonlar:**
    * Sıcaklık, nem ve bulutluluk gibi meteorolojik verilerin eksikliği (Hava durumu tüketimi doğrudan etkiler).
    * Resmi tatiller, bayramlar gibi takvim dışı anomalilerin veri setinde bulunmaması.
  * **Gelecek Çalışmalar:** Meteorolojik verilerin modele eklenmesi ve LSTM/GRU gibi Derin Öğrenme modellerinin entegre edilmesi.
* **Konuşma Notları:**
  > *"Bu çalışmanın pratik hayatta çok geniş uygulama alanları mevcuttur. Geliştirdiğimiz bu yüksek doğruluktaki model, akıllı şebekelerin otomatik yönetiminde, baz yük santrallerinin devreye girip çıkma planlamalarında ve enerji ticaretinde doğrudan kullanılabilir. Projemizin limitasyonlarına gelecek olursak; veri setimizde sıcaklık ve nem gibi meteorolojik veriler bulunmamaktaydı. Oysa biliyoruz ki çok sıcak yaz günlerinde klima kullanımı şebekede ani pikler oluşturur. Gelecek çalışmalarda, bu makine öğrenmesi modeline hava durumu verilerini de entegre etmeyi ve LSTM, GRU gibi derin öğrenme tabanlı zaman serisi modelleriyle performansı daha da ileriye taşımayı hedefliyorum."*

---

### Slayt 15: Sonuç, Özet ve Kapanış (Conclusion & Q&A)
* **Slayt Başlığı:** Sonuç ve Soru-Cevap (Q&A)
* **Slayt İçeriği:**
  * **Özet:**
    * MEE344 kapsamında Türkiye elektrik şebekesi için saatlik tahmin modeli kapsamlı şekilde başarıyla kurulmuştur.
    * Üst düzey özellik mühendisliği (döngüsel zaman, tarihsel gecikmeler, volatilite) tasarlanmıştır.
    * **XGBoost %98.66 R²** ve **Linear Regression %97.13 R²** skoru ile üstün başarı göstermiştir.
    * Kodun ve tüm grafiklerin tam doğrulanabilirliği sağlanmış ve `README.md` kılavuzu hazırlanmıştır.
  * *Teşekkür:* Beni dinlediğiniz için teşekkür ederim. Sorularınızı yanıtlamaktan memnuniyet duyarım.
* **Konuşma Notları:**
  > *"Özetlemek gerekirse, MEE344 Machine Learning dersi kapsamında jürinizin önüne sunduğum bu proje; veri analizi, döngüsel özellik mühendisliği, kronolojik doğrulama stratejisi ve GridSearch optimizasyonlu modelleriyle uçtan uca, hatasız çalışan, endüstriyel standartta bir yük tahmini çalışmasıdır. XGBoost modelimizin elde ettiği %98.66'lık test başarısı ve geliştirdiğim kodların tam tekrar üretilebilir yapısı projemizin akademik kalitesini ortaya koymaktadır. Sunumumu burada tamamlarken, dönem boyunca desteklerini esirgemeyen hocamıza teşekkür ederim. Şimdi varsa sorularınızı yanıtlamaktan memnuniyet duyarım."*
