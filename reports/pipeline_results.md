# MEE344 Proje Model Sonuçları ve Raporu

Bu rapor, geliştirilen uçtan uca makine öğrenmesi yük tahmini boru hattının performans sonuçlarını ve detaylarını içmektedir.

## Veri Seti Özeti
- **Toplam Gözlem Sayısı:** 2064 saatlik veri (yaklaşık 3 ay)
- **Eğitim Kümesi (Train):** %80 (1651 saat, 2025-03-08 00:00:00 ile 2025-05-15 18:00:00 arası)
- **Test Kümesi (Test):** %20 (413 saat, 2025-05-15 19:00:00 ile 2025-06-01 23:00:00 arası)
- **Hedef Değişken:** `Toplam` (Saatlik şebeke toplam elektrik yükü - MWh)

## Geliştirilen Modeller
1. **Doğrusal Regresyon (Linear Regression):** Temel doğrusal model, girdiler standartlaştırılmıştır.
2. **XGBoost Regressor:** 5-Katlı Zaman Serisi Çapraz Doğrulaması (`TimeSeriesSplit`) ile hiperparametreleri optimize edilmiş gelişmiş ağaç tabanlı model.
   - En İyi Parametreler: `{'learning_rate': 0.1, 'max_depth': 3, 'n_estimators': 200, 'subsample': 1.0}`

## Performans Karşılaştırma Tablosu

| Model | Veri Kümesi | R² (Açıklayıcılık Oranı) | RMSE (MWh) | MAE (MWh) |
| :--- | :--- | :---: | :---: | :---: |
| **Doğrusal Regresyon** | Eğitim (Train) | 96.7813% | 1017.76 | 774.83 |
| **Doğrusal Regresyon** | Test | 97.1334% | 925.07 | 711.24 |
| **XGBoost Regressor** | Eğitim (Train) | 99.2580% | 488.68 | 372.44 |
| **XGBoost Regressor** | Test | 98.6644% | 631.43 | 474.74 |

## Temel Bulgular ve Yorumlar
1. **Model Performansları:** 
   - **XGBoost** modeli Test kümesinde **R² = 98.66%** gibi son derece yüksek bir başarıya ulaşmıştır. Bu durum şebekedeki karmaşık, doğrusal olmayan günlük ve haftalık desenlerin model tarafından mükemmel bir şekilde öğrenildiğini kanıtlar.
   - **Doğrusal Regresyon** modelimiz de **R² = 97.13%** gibi yüksek bir temel performans sergilemiştir. Bu başarı, özenle tasarladığımız öznitelik mühendisliğinden (gecikmeler ve döngüsel sin/cos dönüşümleri) kaynaklanmaktadır.
2. **Öznitelik Önem Dereceleri (Feature Importance):**
   - Her iki modelde de en baskın özniteliklerin **`Toplam_lag_1`** (1 saat önceki yük) ve **`Toplam_lag_24`** (1 gün önceki aynı saatteki yük) olduğu görülmüştür. Bu durum elektrik tüketiminin çok yüksek bir oto-korelasyona sahip olduğunu ve dünkü tüketim örüntüsünün bugünkü tüketimi belirlemede en kritik faktör olduğunu göstermektedir.
   - Zaman tabanlı döngüsel özelliklerden günün saati (`Hour_sin`, `Hour_cos`) ve hafta sonu etkisi (`IsWeekend`) modellerin şebekedeki dinamik dalgalanmaları kavramasında kritik rol oynamıştır.

*Grafikler `plots` klasörü altında oluşturulmuş ve kaydedilmiştir.*
