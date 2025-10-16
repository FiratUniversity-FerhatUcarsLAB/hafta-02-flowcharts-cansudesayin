BAŞLA

    // --- GİRİŞ ---
    YAZ "Öğrenci numaranızı giriniz:"
    OKU ogr_no
    YAZ "Şifrenizi giriniz:"
    OKU sifre

    EĞER kimlik_dogrula(ogr_no, sifre) = DOĞRU İSE
        YAZ "Giriş başarılı. Ders kayıt sistemine hoş geldiniz."
    DEĞİLSE
        YAZ "Hatalı giriş. Program sonlandırılıyor."
        BİTİR
    SON

    // --- DERS LİSTESİ GÖRÜNTÜLEME ---
    DERS_LISTESI ← ogrenciye_uygun_dersler(ogr_no)
    YAZ "Alabileceğiniz dersler:"
    HER ders İÇİN DERS_LISTESI
        YAZ ders.ad, "(", ders.kredi, " kredi)"
    SON

    SECILEN_DERSLER ← BOŞ
    toplam_kredi ← 0

    // --- DERS EKLEME/ÇIKARMA DÖNGÜSÜ ---
    TEKRARLA
        YAZ "1. Ders ekle | 2. Ders çıkar | 3. Kayıt onayla"
        OKU secim

        EĞER secim = 1 İSE
            YAZ "Eklemek istediğiniz dersin adını girin:"
            OKU ders_adi

            ders ← dersi_bul(ders_adi)

            // --- KONTROLLER ---

            // 1. Kontenjan kontrolü
            EĞER ders.kontenjan_dolu_mu() İSE
                YAZ "Bu dersin kontenjanı dolu."
                DEVAM ET
            SON

            // 2. Ön koşul kontrolü
            EĞER ders.on_kosul_var_mi() VE tamamlanmamis(ders.on_kosul) İSE
                YAZ "Bu ders için gerekli ön koşul tamamlanmamış."
                DEVAM ET
            SON

            // 3. Zaman çakışması kontrolü
            EĞER zaman_cakisiyor_mu(SECILEN_DERSLER, ders) İSE
                YAZ "Zaman çakışması tespit edildi!"
                DEVAM ET
            SON

            // 4. Kredi limiti kontrolü
            EĞER (toplam_kredi + ders.kredi) > 35 İSE
                YAZ "Kredi limiti aşılıyor! Bu dersi ekleyemezsiniz."
                DEVAM ET
            SON

            // Dersi ekle
            SECILEN_DERSLER ← SECILEN_DERSLER + ders
            toplam_kredi ← toplam_kredi + ders.kredi
            YAZ ders.ad, " eklendi. Toplam kredi: ", toplam_kredi

        EĞER secim = 2 İSE
            YAZ "Çıkarmak istediğiniz dersin adını girin:"
            OKU ders_adi
            SECILEN_DERSLERDEN_KALDIR(ders_adi)
            toplam_kredi ← toplam_kredi - ders_kredisi(ders_adi)
            YAZ ders_adi, " çıkarıldı. Toplam kredi: ", toplam_kredi
        SON

    TA Kİ secim = 3 OLANA KADAR

    // --- DANIŞMAN ONAYI KONTROLÜ ---
    GPA ← ogrenci_gpa(ogr_no)
    EĞER GPA < 2.5 İSE
        YAZ "Danışman onayı gereklidir. Lütfen danışmanınıza bildiriniz."
    DEĞİLSE
        YAZ "Danışman onayı gerekmiyor."
    SON

    // --- KAYIT ÖZETİ VE ONAY ---
    YAZ "Kayıt Özeti:"
    HER ders İÇİN SECILEN_DERSLER
        YAZ ders.ad, " (", ders.kredi, " kredi)"
    SON
    YAZ "Toplam kredi:", toplam_kredi

    YAZ "Kaydı onaylamak istiyor musunuz? (E/H)"
    OKU onay
    EĞER onay = "E" İSE
        YAZ "Kayıt tamamlandı."
    DEĞİLSE
        YAZ "Kayıt iptal edildi."
    SON

BİTİR
