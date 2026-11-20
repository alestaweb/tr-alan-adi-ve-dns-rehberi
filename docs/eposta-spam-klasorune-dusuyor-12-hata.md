# E-postalarınız neden spam klasörüne düşüyor? Alan adı sahipleri için 12 hata

Sitenizin iletişim formu çalışıyor, sipariş onayı gidiyor, bülten gönderiliyor. Ama müşteri "e-posta gelmedi" diyor, sonra spam klasöründe buluyor. Çoğu zaman sorun yazılan metinde değil, alan adının DNS ayarlarındadır. Bu yazı, e-postası kendi alan adından giden (örneğin `info@firmaniz.com.tr`) site sahipleri ve onların sitesini kuran geliştiriciler içindir.

Kısa hatırlatma: alıcı sunucu bir e-postayı aldığında üç soruya bakar.

- **SPF:** Bu e-postayı gönderen sunucu, bu alan adı adına göndermeye yetkili mi?
- **DKIM:** E-posta yolda değiştirilmeden mi geldi, imza alan adına ait mi?
- **DMARC:** SPF ya da DKIM sonucu, kullanıcının gördüğü "Kimden" (From) adresindeki alan adıyla uyuşuyor mu; uyuşmazsa ne yapılsın?

Üçü de alan adınızın DNS'ine eklenen TXT kayıtlarıdır. Aşağıdaki hataların neredeyse hepsi bu üç kayıtla ilgilidir.

## Büyük posta sağlayıcıları ne istiyor

Gmail 1 Şubat 2024'ten beri, Outlook.com (outlook.com, hotmail.com, live.com adresleri) 5 Mayıs 2025'ten beri gönderenlerden kimlik doğrulama istiyor. Özet:

- Gmail'e gönderen herkes için en az SPF ya da DKIM gerekir.
- Günde 5.000'den fazla e-posta gönderenler için SPF, DKIM ve DMARC üçü birden gerekir. DMARC'ta en az `p=none` politikası yeterlidir, ama "Kimden" adresinin alan adı SPF ya da DKIM alan adıyla uyumlu olmalıdır.
- Gmail, pazarlama ve bülten e-postalarında tek tıkla abonelikten çıkma ister; çıkma talebi 48 saat içinde işlenmelidir.
- Gmail, kullanıcıların "spam" olarak işaretleme oranının yüzde 0,1'in altında kalmasını ve hiçbir zaman yüzde 0,3'e ulaşmamasını ister.
- Outlook.com, kurala uymayan yüksek hacimli gönderimleri gereksiz klasörüne almaya başladı ve ileride tamamen reddedeceğini duyurdu.

Günde 5.000 e-posta size uzak görünebilir. Ama sınır gönderen alan adının toplamıdır ve kuralların asıl etkisi şudur: kimlik doğrulaması eksik e-posta artık küçük gönderende de şüpheli sayılıyor.

## 12 sık hata

### 1. SPF kaydı hiç yok

Site yeni kurulmuş, e-posta hesapları açılmış ama DNS'e SPF eklenmemiş. Alıcı sunucu gönderenin yetkili olup olmadığını bilemez. Çözüm: alan adınız adına e-posta gönderen her hizmeti listeleyip tek bir SPF kaydı yazmak.

### 2. Birden fazla SPF kaydı var

Biri e-posta sağlayıcısı için, biri bülten aracı için iki ayrı `v=spf1` kaydı eklenmiş. SPF standardına göre bir alan adında yalnız bir SPF kaydı olabilir; iki kayıt varsa sonuç hata olur ve ikisi de geçersiz sayılır. Doğrusu: bütün göndericileri tek kayıtta birleştirmek.

### 3. SPF'te 10 sorgu sınırı aşılmış

Her `include:` başka bir alan adının kaydını sorgulatır ve onun içindeki `include`'lar da sayılır. Standart, bir SPF kontrolünde en fazla 10 DNS sorgusuna izin verir. Beş altı farklı hizmet eklenince sınır kolayca aşılır ve SPF "kalıcı hata" verir. Kullanmadığınız hizmetleri kayıttan çıkarın; çevrimiçi SPF denetleyicileri toplam sorgu sayısını gösterir.

### 4. Sitenin kendi e-postası listede değil

İletişim formu ve sipariş e-postaları çoğu zaman web sunucusundan gider, posta sunucusundan değil. SPF'te yalnız posta sağlayıcısı yazılıysa sitenin gönderdiği her e-posta kimlik doğrulamasından kalır. İki yol var: web sunucusunun adresini SPF'e eklemek ya da daha iyisi, siteyi kimlik doğrulamalı SMTP ile gerçek posta hesabınız üzerinden göndermeye ayarlamak.

### 5. DKIM açılmamış

Birçok e-posta sağlayıcısı DKIM'i kendiliğinden açmaz; panelde anahtar üretip DNS'e eklemeniz gerekir. DKIM olmadan e-posta yönlendirildiğinde (örneğin alıcı başka adrese iletme kurmuşsa) SPF bozulur ve geriye dayanacak bir şey kalmaz. Her gönderen hizmet için ayrı DKIM kaydı eklenir.

### 6. DKIM kaydı yanlış kopyalanmış

DKIM anahtarı uzun bir metindir. Panele yapıştırırken satır sonu, boşluk ya da tırnak eklenmesi, seçici (selector) adının yanlış yazılması imzayı geçersiz kılar. Ekledikten sonra kendinize bir e-posta gönderin ve iletinin başlık bilgisinde `dkim=pass` yazdığını görün.

### 7. DMARC hiç yok

DMARC kaydı olmayan alan adı, başkalarının sizin adınıza sahte e-posta göndermesine karşı korumasızdır ve büyük sağlayıcılar için de eksik sayılır. Başlangıç için `p=none` politikalı ve rapor adresi içeren bir kayıt yeterlidir. Bu politika hiçbir e-postayı engellemez, yalnız durumu raporlar.

### 8. DMARC hiç sıkılaştırılmamış

`p=none` başlangıçtır, son durak değildir. Birkaç hafta raporları okuyup bütün meşru göndericilerinizin geçtiğini gördükten sonra `p=quarantine`, ardından `p=reject` aşamasına geçilir. Yıllarca `p=none`'da kalan alan adı sahte e-postayı engellemez.

### 9. "Kimden" adresi ile imzalayan alan adı uyuşmuyor

Bülten aracı e-postayı kendi alan adıyla imzalıyor, ama "Kimden" kısmında sizin alan adınız yazıyor. SPF ve DKIM teknik olarak geçer ama DMARC uyumu sağlanmaz. Çözüm: bülten aracında "kendi alan adınla doğrula" adımını tamamlayıp DKIM'in sizin alan adınızla imzalanmasını sağlamak.

### 10. Ücretsiz posta adresiyle "Kimden" yazmak

Siteden giden e-postada gönderen olarak ücretsiz bir posta adresi kullanmak, o adresin sağlayıcısının DMARC politikasına takılır, çünkü e-posta onun sunucusundan çıkmamaktadır. Siteden giden e-postada her zaman kendi alan adınızdaki bir adres kullanın.

### 11. Ters DNS (PTR) ve sunucu adı eksik

E-postayı kendi sunucunuzdan gönderiyorsanız, sunucunun IP adresi geriye doğru sorgulandığında anlamlı bir ad dönmeli ve o ad da aynı IP'ye çözülmelidir. Gmail bunu açıkça ister. Bu kayıt alan adı panelinden değil, sunucunun ya da IP'nin sağlayıcısından ayarlanır.

### 12. Liste temizliği ve abonelikten çıkma

DNS'te her şey doğru olsa bile kimsenin istemediği e-postalar şikâyet alır. Eski, hiç açılmayan, geri dönen adresleri listeden çıkarın. Bültende görünür bir "abonelikten çık" bağlantısı olsun ve talep hemen işlensin. Ticari e-posta gönderiyorsanız alıcıdan önceden onay almış olmanız ve bunu kayıtla gösterebilmeniz de ayrıca yasal bir zorunluluktur.

## Nasıl kontrol edilir

- Gmail'de kendinize gelen bir e-postayı açıp "Orijinali göster" seçeneğine tıklayın. SPF, DKIM ve DMARC için ayrı ayrı "PASS" görmelisiniz.
- Google Postmaster Tools'a alan adınızı ekleyin. Spam şikâyet oranı ve kimlik doğrulama sonuçları burada günlük olarak görünür.
- DMARC raporlarını okumak için bir rapor adresi açın ve haftada bir göz atın. Tanımadığınız bir gönderen görürseniz ya meşru bir hizmeti unutmuşsunuzdur ya da biri adınıza sahte e-posta gönderiyordur.
- Outlook.com'a giden e-postalar geri dönüyorsa hata iletisindeki kodu not edin; Microsoft'un destek sayfaları kodlara göre çözüm verir.

## Kontrol listesi

- [ ] Alan adı adına e-posta gönderen bütün hizmetlerin listesi çıkarıldı (posta sağlayıcısı, web sitesi, bülten aracı, fatura/kargo bildirimleri)
- [ ] Tek bir SPF kaydı var ve bütün göndericiler içinde
- [ ] SPF toplam DNS sorgusu 10'u geçmiyor
- [ ] Web sitesi e-postayı kimlik doğrulamalı SMTP ile gönderiyor
- [ ] Her gönderen hizmet için DKIM açık ve test e-postasında `dkim=pass` görünüyor
- [ ] DMARC kaydı var, rapor adresi tanımlı
- [ ] DMARC politikası planlı biçimde `quarantine`/`reject` aşamasına taşındı
- [ ] "Kimden" adresi her zaman kendi alan adınızda
- [ ] Kendi sunucunuzdan gönderiyorsanız ters DNS kaydı doğru
- [ ] Bültende tek tıkla abonelikten çıkma var, talepler 48 saat içinde işleniyor
- [ ] Spam şikâyet oranı düzenli izleniyor
- [ ] Geri dönen ve hiç açılmayan adresler düzenli temizleniyor

## Resmî kaynaklar

- Google — E-posta gönderen yönergeleri: https://support.google.com/a/answer/81126
- Google — Gönderen yönergeleri SSS: https://support.google.com/a/answer/14229414
- Google Postmaster Tools: https://postmaster.google.com/
- Microsoft — Outlook.com yüksek hacimli gönderen gereksinimleri: https://techcommunity.microsoft.com/blog/microsoftdefenderforoffice365blog/strengthening-email-ecosystem-outlook%E2%80%99s-new-requirements-for-high%E2%80%90volume-senders/4399730
- SPF standardı (RFC 7208): https://www.rfc-editor.org/rfc/rfc7208
- DMARC standardı (RFC 7489): https://www.rfc-editor.org/rfc/rfc7489

---

Hazırlayan: AlestaSoft
