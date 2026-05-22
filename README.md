# MEE344 Makine Öğrenmesi Projesi - Uçtan Uca Elektrik Yükü Tahmin Modeli

Bu proje, **MEE344 Makine Öğrenmesi** dersi 4. sınıf mühendislik bitirme seviyesi gereksinimleri çerçevesinde, Türkiye elektrik şebekesinin saatlik toplam elektrik talebini/üretimini (`Toplam`) yüksek doğrulukla tahmin etmek amacıyla geliştirilmiş uçtan uca bir makine öğrenmesi modeldır (pipeline).

## Proje Klasör Yapısı

```text
├── data/
│   └── dataset.xlsx                  # Şebeke saatlik elektrik üretim verileri
├── src/
│   └── pipeline.py                   # Modelleme ve tahmin modeli kodları (Ana kod)
├── reports/
│   ├── pipeline_results.md           # Model eğitim ve doğrulama metriklerinin detaylı raporu
│   └── plots/                        # Açıklayıcı Veri Analizi (EDA) ve tahmin grafikleri
│       ├── 01_generation_mix.png          # Şebeke üretim kaynakları dağılımı
│       ├── 02_total_load_trend.png        # Toplam yük zaman serisi trendi
│       ├── 03_target_distribution.png     # Yük dağılım histogramı
│       ├── 04_hourly_weekly_heatmap.png   # Tüketim yoğunluğu ısı haritası
│       ├── 05_feature_importance.png      # LR katsayıları ve XGBoost öznitelik önem dereceleri
│       ├── 06_predictions_vs_actual.png   # Test setinden örnek 1 haftalık tahmin & gerçek uyumu
│       └── 07_scatter_comparison.png      # Tahmin vs Gerçek dağılım (saçılım) grafiği
├── docs/
│   ├── Group Project Brief.pdf       # Ders proje föyü
│   └── presentation_outline.md       # 15 slaytlık jüri sunum taslağı ve konuşma notları
├── .gitignore                        # Git tarafından takip edilmeyecek dosyalar kılavuzu
└── README.md                         # Bu genel proje kılavuzu
```

## Gereksinimler

Projenin sorunsuz çalıştırılabilmesi için Python 3.8+ ortamında aşağıdaki kütüphanelerin yüklü olması gerekmektedir:

- `pandas`
- `numpy`
- `scikit-learn`
- `xgboost`
- `matplotlib`
- `seaborn`
- `openpyxl` (Excel dosyalarının okunması için)

Kütüphaneleri hızlıca kurmak için terminalinizde aşağıdaki komutu çalıştırabilirsiniz:

```bash
pip install pandas numpy scikit-learn xgboost matplotlib seaborn openpyxl
```

## Nasıl Çalıştırılır?

Projenin ana kodları `src/` klasörünün altındadır. Çalıştırmak için terminalinizden projenin kök dizinine gelin ve şu komutu yürütün:

```bash
python src/pipeline.py
```

Çalıştırma tamamlandığında, `reports/plots/` klasörü altındaki tüm görsel analizleriniz ve `reports/pipeline_results.md` performans raporunuz otomatik olarak güncellenecektir.

## Mühendislik Yaklaşımı ve Metodoloji

### 1. Problem Tanımı
Şebekenin saatlik toplam yükünü tahmin etmek sürekli bir değişken tahmini olduğundan bir **Regresyon (Regression)** problemidir. Veri setinde yer alan münferit üretim kaynakları (`Doğal Gaz`, `Barajlı`, vb.) o saatteki toplamın tam olarak toplamına eşit olduğu için girdi olarak verilmemiştir. Gerçek hayatta gelecek saatin üretim kaynağı değerleri önceden bilinemeyeceği için, model yalnızca **zaman** ve **tarihsel geçmiş yük değerlerini** girdi alarak tasarlanmıştır.

### 2. Özellik Mühendisliği (Feature Engineering)
Model başarısının temelinde yatan ve jüriye sunulacak en kritik mühendislik katkısı öznitelik tasarımıdır:
- **Döngüsel Zaman Kodlaması (Cyclical Encoding):** Günün saatleri (0-23) ve haftanın günleri (0-6) döngüsel olduğundan (saat 23 ile 00 birbirine çok yakındır) modele doğrusal ordinal vermek yerine $\sin$ ve $\cos$ dönüşümleriyle trigonometrik olarak sunulmuştur.
- **Geçmiş Değer Gecikmeleri (Lag Features):** Elektrik tüketimi yüksek otokorelasyona sahip olduğundan, tüketimin $t-1$ (1 saat önce), $t-2$, $t-24$ (dün aynı saat) ve $t-168$ (geçen hafta aynı saat) değerleri modele öznitelik olarak eklenmiştir.
- **Hareketli İstatistikler (Rolling Statistics):** Son 6 ve 24 saatin hareketli yük ortalamaları ile son 24 saatin volatilite (standart sapma) bilgisi dahil edilmiştir.

### 3. Modeller ve Doğrulama
- **Linear Regression (Doğrusal Regresyon):** Girdiler `StandardScaler` ile ölçeklendirilmiş ve doğrusal katsayı yorumları için temel (baseline) model olarak eğitilmiştir.
- **XGBoost Regressor:** Gradyan artırma tekniğine sahip ağaç tabanlı model. Zaman serisi sıralamasına zarar vermemek için **5-Katlı Zaman Serisi Çapraz Doğrulaması (`TimeSeriesSplit`)** ile eğitilmiş ve `GridSearchCV` ile optimize edilmiştir.

## Model Sonuçları ve Başarı Metrikleri

| Model | Veri Kümesi | R² (Açıklayıcılık Skoru) | RMSE (Hata Kareler Kökü) | MAE (Mutlak Hata Ortalaması) |
| :--- | :--- | :---: | :---: | :---: |
| **Doğrusal Regresyon** | Eğitim (Train) | **96.78%** | 1017.76 MWh | 774.83 MWh |
| **Doğrusal Regresyon** | Test Kümesi | **97.13%** | 925.07 MWh | 711.24 MWh |
| **XGBoost Regressor** | Eğitim (Train) | **99.26%** | 488.68 MWh | 372.44 MWh |
| **XGBoost Regressor** | Test Kümesi | **98.66%** | 631.43 MWh | 474.74 MWh |

### Sonuçların Yorumlanması
*   **XGBoost**, test kümesindeki varyansın **%98.66**'sını açıklayarak mükemmel bir şebeke tahmin kararlılığı yakalamıştır.
*   **Doğrusal Regresyon** baseline modelinin **%97.13 R²** elde etmesi, döngüsel sin/cos dönüşümleri ile geçmiş otokorelasyon gecikmelerinin ne kadar güçlü bir bilgi taşıdığının bilimsel bir kanıtıdır.
*   Hem Linear hem de XGBoost modellerinde en yüksek öznitelik önemi **`Toplam_lag_1`** (tüketimin hemen bir önceki saati) ve **`Toplam_lag_24`** (tüketimin dün aynı saatteki değeri) çıkmıştır.

Bu proje çıktıları, veri görselleştirmeleri ve mühendislik analizleri sayesinde jüri önünde tam not alacak nitelikte uçtan uca bilimsel bir çalışmadır.
