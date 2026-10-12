# .tr Alan Adı ve DNS Rehberi — Web Sitesi Sahipleri İçin

Bir web sitesinin en az konuşulan ama en kolay kaybedilen parçası alan adıdır. Tasarım
yenilenir, sunucu değişir, yazılım güncellenir; alan adı ise yıllarca aynı kalır ve kimse
ona bakmaz. Ta ki yenileme e-postası artık kullanılmayan bir adrese gidene, site bir sabah
açılmayana ya da kurumsal e-postalar sessizce kaybolmaya başlayana kadar.

Bu depo, `.tr` uzantılı (ve genel olarak her) alan adının **kimin adına, nasıl alınacağını,
nasıl yaşatılacağını ve DNS tarafında neyin doğru kurulması gerektiğini** sade bir dille
anlatıyor. Hedef kitle teknik ekip değil; web sitesi sahibi, işletme yöneticisi ve onlara
hizmet veren web geliştiricisi.

**Firma önerisi yok, fiyat yok.** Ücretler ve kayıt kuruluşu seçimi değişkendir; burada
anlatılan, hangi kayıt kuruluşunu seçerseniz seçin sorulması gereken sorulardır.

Hazırlayan: [Alesta WEB](https://alestaweb.com) — 2004'ten beri haber yazılımı ve web
yazılımı geliştiriyor.

---

## İçindekiler

1. Alan adı, DNS, hosting: üç ayrı şey
2. `.tr` sistemi nasıl işliyor (TRABİS)
3. Uzantı seçimi: `.com.tr`, `.com`, ötekiler
4. İsim kuralları ve yasaklı/kısıtlı adlar
5. Alan adı kimin adına alınmalı
6. Kayıt kuruluşu hesabının güvenliği
7. Süre, yenileme ve "unutulan alan adı"
8. Kayıt kuruluşu değiştirme ve devir
9. DNS temelleri: ad sunucuları ve kayıtlar
10. E-posta için DNS: MX, SPF, DKIM, DMARC
11. SSL ve CAA kaydı
12. TTL ve sunucu taşıma günü
13. DNSSEC
14. Uyuşmazlık: adınızı başkası aldıysa
15. Sessizce yanlış giden 12 şey
16. 20 maddelik kontrol listesi
17. İlgili rehberler ve resmî kaynaklar

---

## 1. Alan adı, DNS, hosting: üç ayrı şey

Çoğu sorun bu üçünün tek bir şey sanılmasından çıkar.

| Kavram | Ne işe yarar | Nerede yönetilir |
|---|---|---|
| **Alan adı** | `ornek.com.tr` adının size ait olduğunu gösteren kayıt | Kayıt kuruluşu (registrar) paneli |
| **DNS** | Bu adın hangi sunucuya, hangi e-posta sunucusuna gideceğini söyleyen kayıtlar | Ad sunucularının (NS) bulunduğu yer: kayıt kuruluşu, hosting firması ya da ayrı bir DNS hizmeti |
| **Hosting / sunucu** | Sitenin dosyalarının ve veritabanının çalıştığı makine | Hosting firması veya kendi sunucunuz |

Üçü aynı firmada olabilir, üç ayrı firmada da olabilir. Önemli olan, **hangisinin nerede
olduğunu bilmek**. "Siteyi taşıyoruz" denildiğinde çoğu zaman yalnızca hosting değişir;
alan adı yerinde kalır, yalnızca DNS kayıtları yeni sunucuyu gösterecek şekilde güncellenir.

---

## 2. `.tr` sistemi nasıl işliyor (TRABİS)

`.tr` ile biten alan adları **TRABİS** ("tr" Ağ Bilgi Sistemi) üzerinden yönetilir. Sistem
**Bilgi Teknolojileri ve İletişim Kurumu (BTK)** sorumluluğunda çalışır. 2022'den önce bu
görevi ODTÜ bünyesindeki nic.tr yürütüyordu.

Pratikte bilmeniz gerekenler:

- Başvuru TRABİS'e doğrudan yapılmaz; **BTK tarafından yetkilendirilmiş kayıt
  kuruluşları** üzerinden yapılır. Ücret de kayıt kuruluşuna ödenir.
- `.com.tr`, `.net.tr`, `.org.tr` artık **belgesiz** tahsis edilir: belge istenmez, ilk
  başvuran alır. Eskiden marka veya ticaret unvanı belgesi gerekiyordu.
- Bazı uzantılar hâlâ **belgelidir** (ör. `.av.tr`, `.edu.tr`, `.gov.tr`, `.k12.tr`,
  `.bel.tr`). Hangi belgenin istendiği İnternet Alan Adları Yönetmeliği ekinde yazar.
- **Anonim başvuru yoktur**: kimlik ve iletişim bilgisi zorunludur. Eksik veya yanlış
  bilgi, alan adının iptal gerekçesidir.
- 5809 sayılı Elektronik Haberleşme Kanunu'na göre internet alan adları **haczedilemez**.

`.com`, `.net` gibi genel uzantılar TRABİS'e bağlı değildir; uluslararası kayıt
sistemleri üzerinden işler. Aşağıdaki DNS ve güvenlik bölümleri ise uzantıdan bağımsızdır.

---

## 3. Uzantı seçimi: `.com.tr`, `.com`, ötekiler

Doğru cevap işletmeye göre değişir, ama sorular aynıdır:

- **Hedef kitle Türkiye mi?** `.com.tr` yerel algı ve güven açısından iyi bir tercihtir.
- **Yurt dışına satış veya hizmet var mı?** `.com` daha tanıdıktır.
- **Marka koruması:** Önemli bir markada hem `.com.tr` hem `.com` alınıp biri diğerine
  **301 yönlendirmesi** ile bağlanır. İkisinde aynı içeriği ayrı ayrı yayınlamak arama
  motorlarında kopya içerik sorunu yaratır.
- **Yazım hataları:** Tireli/tiresiz, Türkçe harfin İngilizce karşılığıyla yazılmış
  biçimler ("gunduz" / "gündüz" okunuşu) gerçekten karışıyorsa onları da almak ucuz bir
  sigortadır.

Belgesiz tahsisle birlikte `.com.tr` artık "ilk gelen alır". Marka adınız boşta duruyorsa
beklemek risklidir.

---

## 4. İsim kuralları ve yasaklı/kısıtlı adlar

TRABİS'in yayımladığı kurallara göre alan adının nokta öncesi kısmı:

- Yalnızca `a–z`, `0–9` ve tire (`-`) içerebilir. **Türkçe karakter kullanılamaz.**
- 2 ile 63 karakter arasında olmalıdır.
- Tire ile başlayıp bitemez; 3. ve 4. karakterde yan yana iki tire olamaz.

İki özel liste vardır:

- **TAKAL** (tahsisi yasak adlar): hukuka, kamu düzenine, millî güvenliğe, genel ahlaka
  aykırı adlar.
- **TAKIL** (tahsisi kısıtlı adlar): tarihî/kültürel adlar, il adları ve sahte finans veya
  kamu sitelerini önlemek için "banka", "finans", "bakanlık" gibi terimler.

Adınız bu listelere takılıyorsa kayıt kuruluşu başvuruyu kabul etmez; bu durumda belgeli
yol veya farklı bir ad gerekir.

---

## 5. Alan adı kimin adına alınmalı

Bu rehberin en önemli maddesi budur.

**Alan adı, işletmenin (veya şirket sahibinin) adına kayıtlı olmalı; web ajansının,
çalışanın ya da serbest çalışan geliştiricinin adına değil.**

Sık görülen senaryo: alan adı yıllar önce bir çalışan veya ajans tarafından "kolaylık
olsun" diye kendi hesabıyla alınır. Çalışan ayrılır, ajansla yollar ayrılır, hesap
şifresi kimsede yoktur. Alan adı hukuken işletmenin değildir ve geri almak zaman alır.

Kontrol etmeniz gerekenler:

| Alan | Doğru olan |
|---|---|
| **Alan adı sahibi (registrant)** | Şirket unvanı veya şirket sahibi |
| **Yönetici iletişim e-postası** | Kişiye bağlı olmayan, işletmenin kalıcı bir adresi (ör. `alanadi@` veya `bilgi@`) |
| **Kayıt kuruluşu hesabı** | İşletmenin hesabı; ajansa gerekiyorsa ayrı yetki verilir |
| **Teknik iletişim** | Web ajansı olabilir — sahiplik değil, yalnızca teknik iletişim |

Web geliştirici iseniz: müşterinin alan adını **müşterinin adına** almak, kısa vadede
uğraştırıcı görünse de uzun vadede sizi de korur.

---

## 6. Kayıt kuruluşu hesabının güvenliği

Alan adını ele geçiren, siteyi ve e-postayı ele geçirir: DNS'i değiştirip ziyaretçileri
başka yere yönlendirebilir, e-postaları okuyabilir, hatta "şifremi unuttum" akışlarıyla
başka hesaplarınızı da devralabilir.

- Kayıt kuruluşu hesabında **iki adımlı doğrulama** açık olsun.
- Hesap e-postası, alan adının **kendisi üzerinde olmayan** bir adres olsun. Alan adı
  çalışmazsa o adrese gelen şifre sıfırlama e-postası da gelmez.
- Panelde **transfer kilidi** (registrar lock) seçeneği varsa açık tutun.
- Panele kimlerin erişebildiğini yılda bir gözden geçirin; ayrılan çalışanların ve eski
  ajansların erişimini kaldırın.

---

## 7. Süre, yenileme ve "unutulan alan adı"

`.tr` alan adları **en az 1, en fazla 5 yıllık** süreyle tahsis edilir. Kayıt kuruluşu
bitişten **en az 3 ay önce** e-posta ile uyarır. Yenileme sonrası toplam süre 5 yılı
geçemez.

Alan adı kaybetmenin en yaygın yolu kötü niyet değil, **unutmaktır**:

- Uyarı e-postası artık kimsenin okumadığı bir adrese gider.
- Kayıtlı kredi kartının süresi dolmuştur, otomatik yenileme sessizce başarısız olur.
- "Ajans yeniliyordu" sanılır, ajans "müşteri yeniliyordu" sanar.

Öneriler:

- Önemli alan adlarını **birkaç yıllığına** yenileyin; tek yıllık döngü unutma riskini artırır.
- Bitiş tarihini işletmenin ortak takvimine ekleyin; yalnızca kayıt kuruluşunun e-postasına
  güvenmeyin.
- Otomatik yenileme açıksa ödeme yönteminin geçerliliğini yılda bir kontrol edin.

Sahibi tarafından vazgeçilen (feragat edilen) `.tr` alan adı 2 ay kullanım dışı kalır; bu
sürede sahibi yenileme talep edebilir, etmezse ad yeniden tahsise açılır. Bitiş sonrası
süreçler uzantıya ve kayıt kuruluşuna göre değiştiği için **bitişe bırakmamak** tek güvenli
yoldur.

---

## 8. Kayıt kuruluşu değiştirme ve devir

İki farklı işlem karıştırılır:

| İşlem | Ne değişir | Ne değişmez |
|---|---|---|
| **Kayıt kuruluşu değişikliği (transfer)** | Alan adının yönetildiği firma | Sahibi |
| **Devir** | Alan adının sahibi (ör. şahıstan şirkete) | Gerekirse kayıt kuruluşu aynı kalabilir |

Transfer öncesi:

- Transfer kilidini kaldırın, gerekiyorsa yetki/transfer kodunu alın.
- **DNS'in nerede tutulduğunu** öğrenin. DNS eski kayıt kuruluşunun ad sunucularındaysa ve
  transferle birlikte o hizmet kapanırsa site ve e-posta durur. Transferden önce DNS
  kayıtlarının tam bir kopyasını alın.
- Bitişe çok az kalmış bir alan adını transfere başlatmayın; önce yenileyin.

Alan adı "dondurulmuşsa" (mahkeme veya uyuşmazlık süreci gibi nedenlerle) bilgileri
değiştirilemez, devredilemez ve taşınamaz; yalnızca yenilenebilir.

---

## 9. DNS temelleri: ad sunucuları ve kayıtlar

**Ad sunucuları (NS)**, alan adınızın DNS kayıtlarının hangi sunucularda tutulduğunu söyler.
Kayıt kuruluşu panelinde girilen değer budur. Asıl kayıtlar ise o ad sunucularında durur.

En sık kullanılan kayıtlar:

| Kayıt | Görevi | Örnek |
|---|---|---|
| `A` | Adı bir IPv4 adresine bağlar | `ornek.com.tr → 203.0.113.10` |
| `AAAA` | Adı bir IPv6 adresine bağlar | `ornek.com.tr → 2001:db8::10` |
| `CNAME` | Bir adı başka bir ada yönlendirir | `www → ornek.com.tr` |
| `MX` | E-postanın hangi sunucuya gideceği | `10 mail.ornek.com.tr` |
| `TXT` | Doğrulama ve e-posta güvenliği (SPF, DKIM, DMARC) | `v=spf1 ...` |
| `CAA` | Hangi sertifika otoritesinin SSL verebileceği | `0 issue "letsencrypt.org"` |

Pratik kurallar:

- **Kök alan adına (`ornek.com.tr`) CNAME konmaz**; orada `A`/`AAAA` kullanılır.
- `www` ve kök alan adından **biri asıl adres** olmalı, diğeri ona 301 ile yönlenmeli.
- DNS'i değiştirmeden önce mevcut kayıtların **ekran görüntüsünü veya dışa aktarımını**
  alın. "Yalnızca A kaydını değiştirdik" denilen taşımalarda MX veya TXT kayıtlarının
  silinmesi en sık rastlanan e-posta kesintisi nedenidir.

---

## 10. E-posta için DNS: MX, SPF, DKIM, DMARC

Kurumsal e-postanın teslim edilmesi büyük ölçüde DNS'e bağlıdır. Büyük e-posta
sağlayıcıları bu kayıtları eksik olan alan adlarından gelen postaları giderek daha sık
istenmeyen e-postaya atıyor veya reddediyor.

| Kayıt | Ne söyler | Eksikse |
|---|---|---|
| **MX** | Bu alan adına gelen posta hangi sunucuya teslim edilir | Gelen posta hiç ulaşmaz |
| **SPF** (`TXT`) | Bu alan adı adına hangi sunucular posta gönderebilir | Gönderdiğiniz posta sahte sanılabilir |
| **DKIM** (`TXT`) | Gönderilen postanın dijital imzasını doğrulayan anahtar | İmza doğrulanamaz, güven düşer |
| **DMARC** (`TXT`, `_dmarc`) | SPF/DKIM tutmazsa alıcı ne yapsın, raporlar nereye gitsin | Sahte gönderimler görünmez kalır |

Dikkat edilecekler:

- **Tek SPF kaydı** olmalı. İki ayrı `v=spf1` kaydı varsa ikisi de geçersiz sayılabilir;
  birden fazla gönderici varsa aynı kayıtta birleştirilir.
- Web sitesinin gönderdiği e-postalar (iletişim formu, sipariş bildirimi, şifre sıfırlama)
  da SPF'te tanımlı bir sunucudan çıkmalıdır; aksi hâlde bu e-postalar spam klasörüne düşer.
- DMARC'a `p=none` ile başlayıp raporları izledikten sonra `quarantine`/`reject`'e geçmek
  güvenli bir yoldur.

---

## 11. SSL ve CAA kaydı

SSL sertifikası, alan adı üzerinde **alan adı doğrulaması** ile verilir: sertifika otoritesi,
başvuranın alan adını gerçekten kontrol ettiğini DNS kaydı veya site üzerindeki bir dosya ile
doğrular. Bu yüzden DNS yanlışsa SSL de yenilenemez.

- **CAA kaydı**, alan adınız için hangi sertifika otoritelerinin sertifika verebileceğini
  sınırlar. Kullandığınız otoriteyi yazmayı unutursanız yenileme başarısız olur.
- Ücretsiz sertifikaların süresi kısalıyor (Let's Encrypt 2027'de 64 güne, 2028'de 45 güne
  geçiyor). Yenileme **tamamen otomatik** olmalı ve başarısızlıkta birine haber vermeli.
- Site taşınırken DNS yeni sunucuyu göstermeden yeni sunucuda sertifika alınamaz (DNS
  doğrulaması kullanılmıyorsa). Taşıma planına bu sırayı ekleyin.

---

## 12. TTL ve sunucu taşıma günü

Her DNS kaydının bir **TTL** (yaşam süresi) değeri vardır: dünyadaki DNS sunucuları o
kaydı bu süre boyunca önbellekte tutar. TTL 1 gün ise, değişiklik bazı ziyaretçilere
1 gün boyunca ulaşmayabilir.

Taşıma günü için:

1. Taşımadan **en az bir TTL süresi önce** ilgili kayıtların TTL'ini düşürün (ör. 300 saniye).
2. Yeni sunucuyu hazırlayın, `hosts` dosyasıyla kendi bilgisayarınızdan test edin.
3. Kaydı değiştirin; kısa TTL sayesinde geçiş dakikalar içinde yayılır.
4. Bir süre eski sunucuyu kapatmayın; eski adresi önbellekte tutan ziyaretçiler orada kalır.
5. Her şey yerine oturunca TTL'i normal değerine geri çekin.

"DNS'in yayılması 48 saat sürer" cümlesi çoğu zaman yüksek TTL'in sonucudur; planlanırsa
geçiş çok daha kısadır.

---

## 13. DNSSEC

DNSSEC, DNS yanıtlarına dijital imza ekler. Ziyaretçinin bilgisayarı, aldığı yanıtın gerçekten
alan adınızın ad sunucularından geldiğini ve yolda değiştirilmediğini doğrulayabilir.

- Kendiliğinden açılmaz: bölgenin (zone) imzalanması ve imza bilgisinin (DS kaydı) kayıt
  kuruluşu üzerinden üst bölgeye bildirilmesi gerekir.
- En sık hata: DNSSEC açıkken ad sunucularını başka firmaya taşımak ve eski DS kaydını
  kaldırmamak. Sonuç, doğrulama yapan çözücülerde **alan adının tamamen çözülememesi**dir.
  Ad sunucusu değişikliğinden önce DNSSEC durumunu kontrol edin.

---

## 14. Uyuşmazlık: adınızı başkası aldıysa

Markanız veya ticaret unvanınızla aynı ya da benzer bir `.tr` alan adını başkası kötü
niyetle almışsa, mahkemeye ek olarak **Uyuşmazlık Çözüm Mekanizması (UÇM)** yoluna
başvurabilirsiniz. TRABİS'e göre üç şartın birlikte sağlanması gerekir:

1. Alan adı sizin markanız, ticaret unvanınız, işletme adınız veya kullandığınız başka bir
   tanıtıcı işaretle **aynı veya benzer** olmalı.
2. Alan adını alanın bu adla **hukuki bir hakkı veya bağlantısı** olmamalı.
3. Alan adı **kötü niyetle** alınmış veya kullanılıyor olmalı.

UÇM, BTK'nın yetkilendirdiği uyuşmazlık çözüm hizmet sağlayıcıları üzerinden yürür,
mahkemeye göre daha hızlı ve genellikle daha az masraflıdır; mahkeme yolu her zaman
açıktır. En iyi koruma ise yine önleyicidir: markanızın önemli uzantılarını önceden almak.

---

## 15. Sessizce yanlış giden 12 şey

1. Alan adı ayrılmış bir çalışanın kişisel hesabında, şifre kimsede yok.
2. Yenileme uyarısı, kapatılmış bir e-posta adresine gidiyor.
3. Otomatik yenileme açık ama kayıtlı kartın süresi dolmuş.
4. Kayıt kuruluşu hesabında iki adımlı doğrulama yok.
5. Hesap e-postası alan adının kendisinde; alan adı düşünce şifre sıfırlanamıyor.
6. Site taşınırken MX ve TXT kayıtları silinmiş, e-posta günlerce gelmemiş.
7. İki ayrı SPF kaydı var; ikisi de geçersiz sayılıyor.
8. İletişim formu e-postaları SPF'te olmayan sunucudan çıkıyor, hepsi spam klasöründe.
9. CAA kaydı kullanılan sertifika otoritesini içermiyor; SSL yenilemesi başarısız.
10. TTL 1 gün; taşıma günü site yarım gün iki sunucu arasında gidip geliyor.
11. DNSSEC açıkken ad sunucusu değişmiş, eski DS kaydı kalmış; alan adı çözülmüyor.
12. `www` ve kök alan adı iki ayrı içerik sunuyor; arama motorunda kopya içerik.

---

## 16. 20 maddelik kontrol listesi

**Sahiplik ve hesap**

- [ ] Alan adı sahibi işletme veya şirket sahibi
- [ ] Yönetici e-postası kişiye bağlı değil, kalıcı bir işletme adresi
- [ ] Kayıt kuruluşu hesabı işletmenin, şifre güvenli bir yerde saklı
- [ ] Hesapta iki adımlı doğrulama açık
- [ ] Hesap e-postası alan adının kendisi üzerinde değil
- [ ] Transfer kilidi açık
- [ ] Hesaba erişimi olanların listesi güncel

**Süre ve yenileme**

- [ ] Bitiş tarihi biliniyor ve ortak takvimde
- [ ] Önemli alan adları çok yıllık yenilenmiş
- [ ] Otomatik yenileme ödeme yöntemi geçerli
- [ ] Marka için gerekli diğer uzantılar alınmış ve 301 ile yönleniyor

**DNS**

- [ ] Ad sunucularının nerede olduğu biliniyor
- [ ] DNS kayıtlarının güncel bir yedeği var
- [ ] Kök alan adı ve `www`'den biri asıl, diğeri 301 ile yönleniyor
- [ ] Normal TTL değerleri makul (taşıma öncesi düşürülüyor)

**E-posta ve SSL**

- [ ] MX kayıtları doğru
- [ ] Tek SPF kaydı var ve sitenin e-posta gönderen sunucusunu da kapsıyor
- [ ] DKIM ve DMARC kayıtları tanımlı
- [ ] CAA kaydı (varsa) kullanılan sertifika otoritesini içeriyor
- [ ] SSL yenilemesi otomatik ve hatada bildirim gönderiyor

---

## 17. İlgili rehberler ve resmî kaynaklar

**Bu hesaptaki diğer rehberler**

- [Türkçe Web Yazılım Rehberi](https://github.com/alestaweb/turkce-web-yazilim-rehberi) — web projesi temel kavramlar
- [KVKK için Web Geliştirici Rehberi](https://github.com/alestaweb/kvkk-icin-web-gelistirici-rehberi) — alan adı kayıt bilgisi de kişisel veridir
- [Haber Sitesi SEO Rehberi](https://github.com/alestaweb/haber-sitesi-seo-rehberi) — yönlendirmeler ve kopya içerik
- [İYS Ticari Elektronik İleti Rehberi](https://github.com/alestaweb/iys-ticari-elektronik-ileti-rehberi) — toplu e-posta gönderimi

**Resmî kaynaklar**

- **TRABİS — "tr" Ağ Bilgi Sistemi** — https://www.trabis.gov.tr
- **TRABİS Sıkça Sorulan Sorular** — https://www.trabis.gov.tr/FAQ
- **Bilgi Teknolojileri ve İletişim Kurumu** — https://www.btk.gov.tr
- **İnternet Alan Adları Yönetmeliği** ve **5809 sayılı Elektronik Haberleşme Kanunu** — https://mevzuat.gov.tr
- **Let's Encrypt — 64 günlük sertifika takvimi** — https://letsencrypt.org/2026/10/07/64-day-certs.html

---

## Katkı

Eksik gördüğünüz, güncelliğini yitirmiş veya yanlış bulduğunuz bir madde varsa issue açın.
Ücret ve kayıt kuruluşu bilgileri bilinçli olarak yazılmadı; güncel bilgi için TRABİS ve
kayıt kuruluşunuzun sayfalarına bakın.

## Lisans

MIT — serbestçe kullanın, çoğaltın, alıntılayın.

---

*Hazırlayan: [Alesta WEB](https://alestaweb.com) · haber yazılımı ve web yazılımı
geliştiricisi · 2004'ten beri*
