BASLA

 // SABİT DEĞERLER (Örnek veriler)
 KULLANICI_PINI = 1234
 HESAP_BAKIYESI = 5000.00
 GUNLUK_LIMIT = 1000.00
 CEKILEN_TOPLAM_BUGUN = 150.00 // Bugün çekilmiş miktar
 PIN_DENEME_HAKKI = 3
 CEKIM_TEKRAR_ET = DOGRU // İşlem tekrarı için başlangıç bayrağı
 BLOKE_DURUMU = YANLIS

 // FİŞ BİLGİLERİ İÇİN DEĞİŞKENLER
 ISLEM_TARIHI = "15/10/2025" // Örnek tarih
 ISLEM_SAATI = "20:03" // Örnek saat
 FIŞ_DURUMU = YANLIS // Fişin basılıp basılmayacağını kontrol eder

 // ANA DÖNGÜ: İşlem Tekrarı Kontrolü
 DONGU CEKIM_TEKRAR_ET EŞİT İSE DOGRU
   
   // ----------------------------------------------------------------------
   // 1. PIN DOĞRULAMA DÖNGÜSÜ
   // ----------------------------------------------------------------------
   PIN_DOGRULANDI = YANLIS
   GECICI_HAK = PIN_DENEME_HAKKI

   DONGU GECICI_HAK > 0 VE PIN_DOGRULANDI EŞİT İSE YANLIS
     YAZ "Lütfen PIN kodunuzu giriniz."
     OKU GIRILEN_PIN

     EĞER GIRILEN_PIN EŞİT İSE KULLANICI_PINI İSE
       PIN_DOGRULANDI = DOGRU
       YAZ "PIN doğru. Hoş geldiniz."
     DEĞİLSE
       GECICI_HAK = GECICI_HAK - 1
       YAZ "Hatalı PIN. Kalan deneme hakkı: " + GECICI_HAK
       
       EĞER GECICI_HAK EŞİT İSE 0 İSE
         BLOKE_DURUMU = DOGRU
         YAZ "Çok sayıda hatalı deneme. Kartınız bloke edilmiştir."
       EĞER-SONU
     EĞER-SONU
   DONGU-SONU

   // ----------------------------------------------------------------------
   // 2. BLOKE KONTROLÜ VE İŞLEM AKIŞI
   // ----------------------------------------------------------------------
   EĞER BLOKE_DURUMU EŞİT İSE DOGRU İSE
     CEKIM_TEKRAR_ET = YANLIS // Bloke ise ana döngüden çık
   DEĞİLSE EĞER PIN_DOGRULANDI EŞİT İSE DOGRU İSE
     
     // 2a. TUTAR GİRİŞİ
     YAZ "Çekmek istediğiniz tutarı giriniz (20 TL'nin katları olmalıdır):"
     OKU CEKILMEK_ISTENEN_TUTAR

     // Fişin basılma durumunu her işlem başında sıfırla
     FIŞ_DURUMU = YANLIS
     
     // 2b. TUTAR KONTROLLERİ
     // 2b.i. 20 TL Katı Kontrolü
     EĞER CEKILMEK_ISTENEN_TUTAR MOD 20 EŞİT DEĞİL İSE 0 İSE
       YAZ "Hata: Lütfen 20 TL'nin katları şeklinde bir tutar giriniz."
     
     DEĞİLSE
       // 2b.ii. Günlük Limit Kontrolü
       KALAN_GUNLUK_LIMIT = GUNLUK_LIMIT - CEKILEN_TOPLAM_BUGUN
       
       EĞER CEKILMEK_ISTENEN_TUTAR > KALAN_GUNLUK_LIMIT İSE
         YAZ "Hata: Günlük çekim limitinizi aşıyorsunuz. Kalan limitiniz: " + KALAN_GUNLUK_LIMIT + " TL."
       
       DEĞİLSE
         // 2b.iii. Bakiye Kontrolü
         EĞER CEKILMEK_ISTENEN_TUTAR > HESAP_BAKIYESI İSE
           YAZ "Hata: Hesap bakiyeniz yetersiz. Mevcut bakiyeniz: " + HESAP_BAKIYESI + " TL."
           
         DEĞİLSE
           // 2b.iv. İŞLEM BAŞARILI
           // Bakiye ve Günlük Toplam Güncelleme
           HESAP_BAKIYESI = HESAP_BAKIYESI - CEKILMEK_ISTENEN_TUTAR
           CEKILEN_TOPLAM_BUGUN = CEKILEN_TOPLAM_BUGUN + CEKILMEK_ISTENEN_TUTAR
           FIŞ_DURUMU = DOGRU // Fiş basılabilir
           
           YAZ "İşlem başarılı. Lütfen paranızı alınız."
           YAZ "Yeni Bakiye: " + HESAP_BAKIYESI + " TL."
           YAZ "Bugün Çekilen Toplam: " + CEKILEN_TOPLAM_BUGUN + " TL."
         EĞER-SONU // Bakiye Kontrolü Sonu
       EĞER-SONU // Günlük Limit Kontrolü Sonu
     EĞER-SONU // 20 TL Katı Kontrolü Sonu

     // ----------------------------------------------------------------------
     // 3. İŞLEM FİŞİ BASIMI
     // ----------------------------------------------------------------------
     EĞER FIŞ_DURUMU EŞİT İSE DOGRU İSE
       YAZ "----------------------------------------"
       YAZ "           İŞLEM FİŞİ"
       YAZ "----------------------------------------"
       YAZ "İşlem Türü: Nakit Çekme"
       YAZ "Tarih/Saat: " + ISLEM_TARIHI + " / " + ISLEM_SAATI
       YAZ "Çekilen Tutar: " + CEKILMEK_ISTENEN_TUTAR + " TL"
       YAZ "Yeni Bakiye: " + HESAP_BAKIYESI + " TL"
       YAZ "Günlük Kalan Limit: " + (GUNLUK_LIMIT - CEKILEN_TOPLAM_BUGUN) + " TL"
       YAZ "----------------------------------------"
       YAZ "İşlem fişiniz hazırlanıyor..."
     EĞER-SONU
     
     // ----------------------------------------------------------------------
     // 4. İŞLEM TEKRARI SEÇENEĞİ
     // ----------------------------------------------------------------------
     YAZ "Başka bir işlem yapmak ister misiniz? (E/H)"
     OKU TEKRAR_SECIM

     EĞER TEKRAR_SECIM EŞİT İSE "H" VEYA TEKRAR_SECIM EŞİT İSE "h" İSE
       CEKIM_TEKRAR_ET = YANLIS
     DEĞİLSE
       YAZ "İşleme devam ediliyor..."
     EĞER-SONU
     
   EĞER-SONU // PIN Doğrulandı Kontrolü Sonu

 DONGU-SONU // Ana Döngü Sonu

 YAZ "Bizi tercih ettiğiniz için teşekkür ederiz. İyi günler dileriz."

BITIR
