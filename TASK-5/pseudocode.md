BASLA

// ---------------------------
// 1. Sistem Başlatma
// ---------------------------
SISTEM_AKTIF = DOGRU
ALARM_DURUMU = KAPALI
ALARM_SEVIYESI = 0
EV_SAHIBI_EVDE = YANLIS

YAZ "Akıllı Ev Güvenlik Sistemi başlatıldı."

// ---------------------------
// 2. Ana Döngü (Sürekli Sensör İzleme)
// ---------------------------
DÖNGÜ SISTEM_AKTIF EŞİT İSE DOGRU
    YAZ "Sistem aktif. Sensörler izleniyor..."

    tehditSayisi = 0
    tetiklenenSensorler = []

    // ---------------------------
    // 2a. Sensör Kontrolleri
    // ---------------------------
    HAREKET_ALGILANDI = HAREKET_SENSORU_OKU()
    EĞER HAREKET_ALGILANDI = DOGRU İSE
        tehditSayisi = tehditSayisi + 1
        tetiklenenSensorler.EKLE("Hareket Sensörü")
    EĞER-SONU

    KAPI_ACILDI = KAPI_SENSORU_OKU()
    PENCERE_ACILDI = PENCERE_SENSORU_OKU()
    EĞER KAPI_ACILDI = DOGRU İSE
        tehditSayisi = tehditSayisi + 1
        tetiklenenSensorler.EKLE("Kapı Sensörü")
    EĞER-SONU
    EĞER PENCERE_ACILDI = DOGRU İSE
        tehditSayisi = tehditSayisi + 1
        tetiklenenSensorler.EKLE("Pencere Sensörü")
    EĞER-SONU

    // Kamera tetiklenmesi (isteğe bağlı)
    EĞER tehditSayisi > 0 İSE
        KAMERA_AKTIF_ET()
        YAZ "Kamera aktif. Görüntü kaydı başlatıldı."
    EĞER-SONU

    // ---------------------------
    // 2b. Yanlış Alarm Kontrolü
    // ---------------------------
    EĞER EV_SAHIBI_EVDE = DOGRU İSE
        YAZ "Ev sahibi evde. Yanlış alarm engellendi."
        ALARM_DURUMU = KAPALI
        ALARM_SEVIYESI = 0
    DEĞİLSE
        // ---------------------------
        // 2c. Alarm Seviyesi Hesaplama
        // ---------------------------
        EĞER tehditSayisi = 1 İSE ALARM_SEVIYESI = 1
        EĞER tehditSayisi = 2 İSE ALARM_SEVIYESI = 2
        EĞER tehditSayisi >= 3 İSE ALARM_SEVIYESI = 3
        YAZ "Tespit edilen tehdit sayısı: " + tehditSayisi
        YAZ "Tetiklenen sensörler: " + tetiklenenSensorler
        YAZ "Alarm seviyesi: " + ALARM_SEVIYESI

        // ---------------------------
        // 2d. Alarm ve Bildirim
        // ---------------------------
        EĞER ALARM_SEVIYESI > 0 İSE
            ALARM_DURUMU = AÇIK
            SESLI_ALARM_CALISTIR(ALARM_SEVIYESI)
            YAZ "Alarm çaldı!"

            SMS_GONDER("Alarm durumu: Seviye " + ALARM_SEVIYESI)
            APP_BILDIRIM_GONDER("Evde hareket tespit edildi!")
            EMAIL_GONDER("Akıllı ev sistemi uyarısı", "Alarm seviyesi: " + ALARM_SEVIYESI)
            YAZ "Bildirimler gönderildi (SMS + Uygulama + E-posta)."
        DEĞİLSE
            YAZ "Her şey normal."
        EĞER-SONU
    EĞER-SONU

    // ---------------------------
    // 2e. Alarm Sıfırlama / Devam Ettirme
    // ---------------------------
    EĞER ALARM_DURUMU = AÇIK İSE
        YAZ "Alarmı sıfırlamak istiyor musunuz? (E/H)"
        OKU CEVAP
        EĞER CEVAP = "E" İSE
            ALARM_DURUMU = KAPALI
            ALARM_SEVIYESI = 0
            YAZ "Alarm sıfırlandı."
        DEĞİLSE
            YAZ "Alarm açık durumda kalıyor."
        EĞER-SONU
    EĞER-SONU

    // ---------------------------
    // 2f. Bekle ve Döngüye Dön
    // ---------------------------
    BEKLE(5 SANİYE)
DÖNGÜ-SONU

BITIR
