// HASTANE RANDEVU SİSTEMİ PSEUDOCODE (MODÜL 1)

BASLA

// 1. Hasta Kimlik Doğrulama
YAZ "TC Kimlik Numaranızı giriniz:"
OKU tc_no

EĞER dogrula_kimlik(tc_no) = DOGRU ISE
    YAZ "Kimlik doğrulama başarılı."
DEGILSE
    YAZ "Kimlik doğrulama başarısız. Sistemden çıkılıyor."
    BITIR
SON_EGER

// 2. İşlem Seçimi
YAZ "İşlem seçiniz: (1) Randevu Al  (2) Tahlil Sonucu Gör"
OKU secim

EĞER secim = 1 ISE
    // 3. Randevu Modülü
    YAZ "Poliklinik seçiniz:"
    OKU poliklinik
    
    DOKTOR_LISTESI = doktorlari_getir(poliklinik)
    DÖNGÜ her doktor IN DOKTOR_LISTESI
        YAZ doktor.id + " - " + doktor.ad
    SON_DÖNGÜ
    
    YAZ "Doktor ID’si seçiniz:"
    OKU secilen_doktor_id
    
    SAAT_LISTESI = uygun_saatler(secilen_doktor_id)
    YAZ "Uygun saatler:"
    DÖNGÜ her saat IN SAAT_LISTESI
        YAZ saat
    SON_DÖNGÜ
    
    YAZ "Randevu almak istediğiniz saati giriniz:"
    OKU secilen_saat
    
    randevu_onayla(tc_no, secilen_doktor_id, secilen_saat)
    gonder_SMS(tc_no, "Randevunuz onaylandı: " + secilen_saat)
    
    YAZ "Randevunuz başarıyla oluşturuldu ve SMS gönderildi."
    
EĞER secim = 2 ISE
    // 4. Tahlil Sonucu Gör
    YAZ "Tahlil sonuçlarınız görüntüleniyor..."
    goster_tahlil_sonuclari(tc_no)
DEGILSE
    YAZ "Geçersiz seçim. Sistemden çıkılıyor."
SON_EGER

BITIR





// TAHLİL MODÜLÜ PSEUDOCODE (MODÜL 2)

BASLA

// 1. Kimlik Doğrulama
YAZ "Lütfen TC Kimlik Numaranızı giriniz:"
OKU tc_no

EĞER dogrula_kimlik(tc_no) = DOGRU ISE
    YAZ "Kimlik doğrulama başarılı."
DEGILSE
    YAZ "Kimlik doğrulama başarısız. Sistemden çıkılıyor."
    BITIR
SON_EGER

// 2. Ana Döngü: İşlem Tekrarı
TEKRAR
    YAZ "---- Tahlil Modülüne Hoşgeldiniz ----"
    
    // 3. Tahlil Kaydı Kontrolü
    EĞER tahlil_var_mi(tc_no) = DOGRU ISE
        YAZ "Tahlil kaydı bulundu."
        
        // 4. Sonuç Hazır mı Kontrolü
        EĞER sonuc_hazir_mi(tc_no) = DOGRU ISE
            YAZ "Tahlil sonuçlarınız hazır."
            
            // 5. Sonuç Görüntüleme
            goster_tahlil_sonuclari(tc_no)
            
            // 6. PDF İndirme Seçeneği
            YAZ "Sonuçları PDF olarak indirmek ister misiniz? (E/H)"
            OKU cevap
            EĞER cevap = "E" ISE
                indir_PDF(tc_no)
                YAZ "PDF başarıyla indirildi."
            DEGILSE
                YAZ "PDF indirme iptal edildi."
            SON_EGER
            
        DEGILSE
            YAZ "Tahlil sonuçlarınız henüz hazır değil. Lütfen daha sonra tekrar deneyiniz."
        SON_EGER
        
    DEGILSE
        YAZ "Sistemde herhangi bir tahlil kaydınız bulunmamaktadır."
    SON_EGER
    
    // 7. İşlem Tekrarı Seçeneği
    YAZ "Başka bir işlem yapmak ister misiniz? (E/H)"
    OKU tekrar
    
SONA_KADAR_DONGU tekrar = "E"

YAZ "Sistemden çıkılıyor. Geçmiş olsun!"
BITIR
