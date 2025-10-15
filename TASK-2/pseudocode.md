BASLA

// 1. Kullanıcı Girişi Kontrolü
YAZ "E-ticaret sistemine hoş geldiniz."
YAZ "Lütfen kullanıcı adı ve şifre giriniz."
OKU kullanici_adi, sifre

EGER dogrula_kullanici(kullanici_adi, sifre) = DOGRU ISE
    YAZ "Giriş başarılı. Hoş geldiniz, " + kullanici_adi
DEGILSE
    YAZ "Giriş başarısız. Misafir olarak devam ediliyor."
SON_EGER


// 2. Ürün Kategorileri Arasında Gezinme (DÖNGÜ)
kategori_secimi = "EVET"
DÖNGÜ kategori_secimi = "EVET" İKEN
    YAZ "Lütfen bir kategori seçiniz: (1) Elektronik (2) Giyim (3) Ev (4) Spor"
    OKU kategori

    YAZ "Seçilen kategorideki ürünler listeleniyor..."
    YAZ "Ürün ID'si ve adetini giriniz."
    OKU urun_id, adet

    // 3. Stok Kontrolü
    EGER stok_kontrol(urun_id, adet) = DOGRU ISE
        sepet.EKLE(urun_id, adet)
        YAZ "Ürün sepete eklendi."
    DEGILSE
        YAZ "Yetersiz stok. Lütfen daha düşük adet seçiniz."
    SON_EGER

    YAZ "Başka kategoriye bakmak ister misiniz? (EVET/HAYIR)"
    OKU kategori_secimi
SON_DÖNGÜ


// 4. Sepeti Görüntüleme ve Düzenleme (DÖNGÜ)
YAZ "Sepetinizdeki ürünler:"
YAZ sepet.LISTELE()

duzenleme = "EVET"
DÖNGÜ duzenleme = "EVET" İKEN
    YAZ "Ürün silmek veya adet değiştirmek ister misiniz? (EVET/HAYIR)"
    OKU duzenleme
    EGER duzenleme = "EVET" ISE
        OKU urun_id, yeni_adet
        EGER stok_kontrol(urun_id, yeni_adet) = DOGRU ISE
            sepet.GUNCELLE(urun_id, yeni_adet)
            YAZ "Sepet güncellendi."
        DEGILSE
            YAZ "Yetersiz stok."
        SON_EGER
    SON_EGER
SON_DÖNGÜ


// 5. İndirim Kodu Uygulama (GÜNCELLENMİŞ)
YAZ "İndirim kodunuz varsa giriniz, yoksa 'YOK' yazınız."
OKU indirim_kodu

EGER indirim_kodu != "YOK" ISE
    KOD_DURUMU = indirim_kontrol(indirim_kodu) // "GECERLI", "GECERSIZ", "SURE_BITMIS"

    EGER KOD_DURUMU = "GECERLI" ISE
        sepet.INDIRIM_UYGULA(indirim_kodu)
        YAZ "İndirim kodu başarıyla uygulandı."

    DEGILSE EGER KOD_DURUMU = "SURE_BITMIS" ISE
        YAZ "Bu kodun kullanım tarihi geçmiştir."

    DEGILSE EGER KOD_DURUMU = "GECERSIZ" ISE
        YAZ "Bu kod geçersizdir."

    SON_EGER
SON_EGER


// 6. Minimum Tutar Kontrolü (KOŞUL)
toplam_tutar = sepet.TOPLAM()
EGER toplam_tutar < 50 ISE
    YAZ "Alışveriş tamamlanamıyor. Minimum 50 TL harcama gereklidir."
    YAZ "Sepetinize ürün ekleyiniz."
    BITIR
SON_EGER


// 7. Kargo Ücreti Hesaplama (KOŞUL: 200 TL Üzeri Ücretsiz)
EGER toplam_tutar >= 200 ISE
    kargo_ucreti = 0
    YAZ "Kargonuz ücretsiz!"
DEGILSE
    kargo_ucreti = 30
    YAZ "Kargo ücreti: 30 TL"
SON_EGER

genel_toplam = toplam_tutar + kargo_ucreti
YAZ "Genel Toplam: ", genel_toplam, " TL"


// 8. Ödeme Yöntemi Seçimi (KOŞUL)
YAZ "Ödeme yöntemini seçiniz: (1) Kart (2) Kapıda Ödeme"
OKU odeme_tipi

EGER odeme_tipi = 1 ISE
    YAZ "Kart bilgilerinizi giriniz:"
    OKU kart_no, son_kullanma, cvv
    EGER odeme_kontrol(kart_no, son_kullanma, cvv, genel_toplam) = DOGRU ISE
        YAZ "Ödeme başarılı."
    DEGILSE
        YAZ "Ödeme reddedildi. İşlem iptal edildi."
        BITIR
    SON_EGER

DEGILSE EGER odeme_tipi = 2 ISE
    YAZ "Kapıda ödeme seçildi. Ödeme teslimatta yapılacaktır."
SON_EGER


// 9. Sipariş Onayı
YAZ "Siparişiniz başarıyla oluşturuldu!"
YAZ "Sipariş numaranız: " + rastgele_siparis_no()
YAZ "Tahmini teslim süresi: 3-5 iş günü"
YAZ "Teşekkür ederiz!"

BITIR
