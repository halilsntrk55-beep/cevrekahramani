# Çevre Kahramanı · Araştırma sürümü

## Oyun

`index.html` tek başına veya GitHub Pages üzerinde çalışır. Akış: ev → mahalle → ambalaj iade makinesi → şehir. Makine aşaması 5 karardan oluşur ve şehirden önce tamamlanır. Her doğru makine kararı +80, yanlış karar en fazla −20 puandır. Gerçek makine kabul ve depozito kuralları bu eğitim modelinden farklı olabilir.

## 34 maddelik endeks

Ön ve son değerlendirme aynı 34 taslak ifadeyi kullanır: su (6), geri dönüşüm (8), enerji (6), doğa/ulaşım (6), makine (8). Beşli katılım ölçeği 1–5 arasıdır. Bütün maddeler yanıtlanmadan endeks hesaplanmaz.

Çevre Kahramanı Endeksi = `100 × Σ(düzeltilmiş yanıt − 1) / (34 × 4)`. Sonuç en yakın tam sayıya yuvarlanır. Alan puanları da aynı normalizasyonu kullanır. Oyun puanı endekse dahil değildir. Ön/son farkı puan farkıdır, yüzde değişim değildir.

Mevcut Google Form sorularına erişilemediği için bunlar o formlardaki soruların kopyası değildir. Google Form yanıtları otomatik aktarılmaz. Bu taslak, doğrulanmış bilimsel ölçek veya nedensel etki ölçümü değildir. Gerçek yaşam etkisi için izlem verisi ayrıca toplanmalıdır.

### Soruları sonradan değiştirme

`index.html` içindeki ikinci script'te `groups` nesnesindeki ifadeleri değiştirin. Dağılım toplamı 34 kalmalıdır. `questions` oluşturulurken üretilen her kaydın `id`, `domain`, `text`, `reverse` alanları vardır. Ters puanlanacak madde kimliklerini `reversedQuestionIds` listesine ekleyin; örneğin `['water-2']` (dönüşüm: `6 − yanıt`). Soru/anahtar değiştiğinde `VERSION` değerini ve kayıt şemasını güncelleyin; farklı ölçek sürümlerini aynı analizde karıştırmayın. Pilot çalışma, uzman incelemesi ve güvenirlik analizi araştırmacı tarafından yapılmalıdır.

## Analitik ve dışa aktarma

Her oyun bir rastgele oturum kimliği alır. Kaydedilenler: karar, görev ve doğru/yanlış durumu, hata konusu, olay zamanı, güncel puan, puan değişimi, aktif bölüm süresi, bölüm deneme/hata sayıları, oyun sonucu, ön/son ham yanıtlar, alan ve toplam endeksleri. Makine adımları ayrıca kaydedilir. Harita süresi görev süresinden ayrıdır; menü/rehber/profil açıkken aktif süre işlemez. Sekme kapanışı bir kazanma veya başarısızlık olarak uydurulmaz; sonuç boş kalabilir. Oturumlar dışa aktarıldıktan sonra kimlikleriyle eşleştirilebilir.

Kayıtlar yalnızca `localStorage` içinde bu cihaz/tarayıcı ve site kökeninde saklanır. Merkezi veritabanı yoktur. Son 20 oturum tutulur. Anketler gönüllüdür ve ad, okul veya öğrenci numarası istenmez. Tarayıcı depolaması çalışmıyorsa ekranda uyarı görünür; dışa aktarma yine çalışır. Sayfa yenilenince önceki oturumlar arşivde kalır; aktif oyun sürdürülmez. Analitik menüsünden tüm oturumları JSON (soru metinleri/yanıtlar dahil) veya CSV (özet + olay satırları) olarak indirin. Cihaz kayıtları aynı menüden silinir. CSV'de formül enjeksiyonuna karşı hücreler kaçışlanır.

## Kişisel geri bildirim ve isteğe bağlı gerçek yapay zekâ

Yerel profil kurallarla çalışır ve açıkça bu şekilde etiketlenir. Su hakkında oyun içi görev olmadığı için bu alana ilişkin çıkarım sadece anket öz bildiriminden yapılır. Yanıtlanmayan alanlarda başarı iddiası üretilmez.

Gerçek yapay zekâ için `server.mjs` aynı kökende `POST /api/environment-feedback` sağlar. Node.js 22 veya üzeri yeterlidir, paket kurulumu gerektirmez:

```powershell
$env:OPENAI_API_KEY = '<sunucu tarafındaki anahtar>'
$env:OPENAI_MODEL = '<hesabınızda kullanılabilir model kimliği>'
node server.mjs
```

Ardından `http://127.0.0.1:4173` adresini açın. Model kimliği açıkça yapılandırılmalıdır; kod model veya API anahtarı uydurmaz. Anahtarı HTML/GitHub içine yazmayın. Sunucu, OpenAI Responses API kullanır ve `store:false` gönderir. Yalnızca alan/özet puanlar, sayısal metrikler ve izinli enerji/yatırım kararları aktarılır; ham anket yanıtları, kimlik veya serbest metin aktarılmaz. İstek yalnızca öğrenci “Yapay zekâ yorumu iste” düğmesine basınca yapılır. Yanıt profil altında ayrıca “Yapay zekâ destekli yorum” olarak görünür. API hatasında mevcut yerel profil korunur.

API veya model yapılandırılmamışsa 503 döner; GitHub Pages Node sunucusu çalıştırmadığından Pages sürümünde yalnızca yerel geri bildirim kullanılabilir. Gerçek AI etkinleştirmek için Node sunucusunun aynı kökende yayınlanması gerekir. Sunucu varsayılan olarak localhost'a bağlanır. Dış yayın için HTTPS, erişim denetimi ve kalıcı kota sınırı ayrıca kurulmalıdır; mevcut sınır tek işlem/IP başına dakikada 5 istektir.

API kaynağı: [OpenAI resmi metin üretimi belgeleri](https://developers.openai.com/api/docs/guides/text).

## Doğrulama

Chrome/Playwright ile uçtan uca doğrulandı: 0/50/100 endeks sınırları, eksik yanıt, ön/son değerlendirme, atık hatası, makine kilidi/yanlış/doğru adımlar, şehir tamamlanması, süre dolması, AI erişim hatası, JSON dışa aktarma, arşiv ve mobil görünüm. Sunucu ayrı testte taklit API yanıtıyla doğrulandı; gerçek API anahtarı olmadığı için ücretli gerçek model çağrısı yapılmadı.


