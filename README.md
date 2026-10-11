# 🧪 rgbanaliz

Biyokimya laboratuvarında klasik UV-Vis spektrofotometresi yerine akıllı telefon kamerası ve görüntü işleme (RGB analizi) kullanarak Bradford yöntemiyle protein konsantrasyonu tahmini.

---

## 💡 Çıkış Hikayesi

Biyokimya labında Bradford yöntemiyle total protein tayini yapıyorduk. 96 kuyucuklu plakada (well-plate) protein konsantrasyonu arttıkça Coomassie Brilliant Blue boyasının kahverengi/kırmızımsı tondan belirgin bir maviye dönüşünü izlerken aklıma şu soru geldi:

> *"Sırada bekleyip pahalı bir UV-Vis spektrofotometresinde 595 nm okuması almak yerine; telefonla çektiğimiz plaka fotoğrafındaki piksellerin RGB değerlerinden bu konsantrasyonları regresyonla modelleyemez miyiz?"*

Laboratuvardan çıkıp bu fikri test etmek için Google Colab üzerinde bu interaktif analiz aracını yazdım.

---

## 🔬 Nasıl Çalışıyor?

1. **Görsel Yükleme:** Plakanın üstten çekilmiş fotoğrafı yüklenir.
2. **İnteraktif Arayüz (Canvas):** Colab içine gömülü HTML5 Canvas arayüzü açılır.
   - Kuyucuktaki menisküs veya kenar yansımalarını dışarıda bırakmak için canlı bir **yarıçap (slider)** ayarı bulunur.
   - Standartlar (`A1-H2`) ve bilinmeyen numuneler (`A3, A4`) sırayla tıklanarak merkezleri işaretlenir.
3. **RGB Maskeleme:** OpenCV ile tıklanan merkez ve yarıçapta dairesel maske oluşturulup kuyucuğun ortalama R, G ve B kanal değerleri çıkarılır.
4. **Kalibrasyon Eğrisi & Regresyon:** BSA standart konsantrasyonları ile ortalama RGB değerleri eşleştirilip çoklu doğrusal regresyon (`LinearRegression`) modeli eğitilir ($R^2$ skoru ve eğri görselleştirilir).
5. **Bilinmeyen Numune Tahmini:** Bilinmeyen kuyucukların RGB verisi modele verilerek konsantrasyonları (mg/mL) tahmin edilir.

---

## 📊 Standart Kalibrasyon Tablosu (BSA)

Testte kullanılan standart BSA (Bovine Serum Albumin) konsantrasyonları ve dublike kuyucuk eşleşmeleri:

| Konsantrasyon (mg/mL) | Kuyucuklar (Dublike) |
|---|---|
| `0.000` (Blank) | A1, A2 |
| `0.031` | B1, B2 |
| `0.062` | C1, C2 |
| `0.125` | D1, D2 |
| `0.250` | E1, E2 |
| `0.500` | F1, F2 |
| `0.750` | G1, G2 |
| `1.000` | H1, H2 |
| **Bilinmeyenler** | **A3, A4** |

---

## 🛠️ Kullanılan Teknolojiler

- **Python:** OpenCV, NumPy, Matplotlib, Scikit-learn, PIL
- **Etkileşim:** IPython Display, HTML5 Canvas, JavaScript (`Promise` & `output.eval_js` köprüsü)
- **Ortam:** Google Colab

---

## 🚀 Kullanım

1. `rgb.ipynb` dosyasını Google Colab'da açın.
2. Hücreyi çalıştırın ve plaka fotoğrafınızı yükleyin.
3. Açılan arayüzde slider ile daire boyutunu sıvının homojen renkli alanına göre ayarlayın.
4. Ekranda belirtilen sırayla kuyucukların merkezine tıklayın (18 tıklama).
5. Tıklamalar bittiğinde kalibrasyon grafiği ve bilinmeyen numunelerin tahmini otomatik olarak ekrana basılır.

---

## 📌 Geliştirme Fikirleri (Roadmap)

- [ ] Yanlış tıklamalara karşı Canvas arayüzüne **"Geri Al" (Undo)** butonu eklemek.
- [ ] Ham RGB yerine Beer-Lambert kanununa uygun absorbans hesabı ($A = -\log_{10}(I / I_0)$) veya HSV/LAB renk uzayı dönüşümü.
- [ ] Ortam ışığı ve flaş farklarını nötrlemek için plakanın beyaz çerçevesinden referans alan beyaz dengesi (White Balance) normalizasyonu.
- [ ] Kuyucuk merkezlerini otomatik tespit eden görüntü işleme filtresi (Hough Circles).