<!-- SPDX-License-Identifier: MIT -->

# Kod Kalitesi Altyapısı

Kalite standardı belirtmek bir **talimattır**; koda uyduğunu görmek ayrı bir **doğrulama**
işidir. Bu dosya ikincisini kurar. Buradaki araçların tamamı ücretsizdir.

## İçindekiler

1. [Neden doğrulama katmanı gerekir](#neden-doğrulama-katmanı-gerekir)
2. [ISO/IEC 5055 ve araç eşlemesi](#isoiec-5055-ve-araç-eşlemesi)
3. [Katman katman kurulum](#katman-katman-kurulum)
4. [Testin rolü](#testin-rolü)
5. [Çapraz denetim](#çapraz-denetim)
6. [CI entegrasyonu](#ci-entegrasyonu)
7. [Kontrol listesi](#kontrol-listesi)

---

## Neden doğrulama katmanı gerekir

Bir modele "şu standarda uy" demek, uyduğunu garanti etmez. Kod gözle bakınca doğru görünüp
pratikte yanlış davranabilir — özellikle asenkron akışlarda, hata dallarında ve kenar
durumlarda. Statik analiz ve testler, "doğru görünüyor" ile "doğru çalışıyor" arasındaki
farkı ölçülebilir hâle getirir.

Bu, üretimin AI ile yapıldığı akışlarda daha da kritiktir: üretim hızı arttıkça, elle
inceleme darboğaza dönüşür.

## ISO/IEC 5055 ve araç eşlemesi

ISO/IEC 5055:2021, kaynak kodun **yapısal** kalitesini dört sütunda ölçen uluslararası bir
standarttır: Reliability (güvenilirlik), Security (güvenlik), Performance Efficiency
(performans verimliliği), Maintainability (bakım yapılabilirlik). Çalışma zamanı davranışını
değil, koddaki kural ihlallerini sayarak ölçer — bu yüzden otomatikleştirilebilir.

Standart nispeten yeni olduğu için tam uyum iddia eden ücretsiz bir araç yoktur. Ancak dört
sütunun kapsamı, ücretsiz araçların birleşimiyle pratikte büyük ölçüde karşılanır:

| Sütun | Ücretsiz araç karşılığı |
|---|---|
| Reliability | Tip sistemi (strict mod), linter kuralları, testler |
| Security | Bağımlılık taraması, güvenlik odaklı linter eklentileri, gizli-anahtar taraması |
| Performance Efficiency | Karmaşıklık limitleri, bundle/boyut analizi |
| Maintainability | Karmaşıklık ve tekrar analizi, ölü kod tespiti, format tutarlılığı |

Kullanıcıya "ISO 5055 sertifikalı" bir kurulum vaat etme; "dört sütunu pratikte karşılayan
ücretsiz bir doğrulama katmanı" doğru ifadedir.

## Katman katman kurulum

Sırayla kur, her katmanı çalıştığını doğrulayarak. Hepsini birden ekleyip sonra hata
ayıklamak, hangi aracın neyi yakaladığını belirsizleştirir.

### 1. Tip katmanı

Dil destekliyorsa en katı modu aç. Bu, Reliability sütununun büyük kısmını derleme anında
kapatır ve hiçbir çalışma zamanı maliyeti yoktur.

TypeScript örneği — `tsconfig.json`:
```json
{
  "compilerOptions": {
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "noImplicitOverride": true
  }
}
```

### 2. Linter

Stil değil, **hata sınıfı** yakalayan kuralları önceliklendir: kullanılmayan değişkenler,
ulaşılamayan kod, güvensiz tip dönüşümleri, aşırı karmaşıklık.

Karmaşıklık limiti örneği (ESLint):
```json
{
  "rules": {
    "complexity": ["warn", 10],
    "max-depth": ["warn", 4]
  }
}
```

### 3. Statik analiz

SonarQube Community Edition (self-hosted) veya SonarQube for IDE, dosya bazında
Reliability / Security / Maintainability derecelendirmesi üretir. Bu, "kod kaliteli mi?"
sorusuna sayısal cevap veren katmandır.

Alternatif olarak dil ekosisteminin kendi analiz aracı da kullanılabilir; önemli olan
**sayısal ve tekrarlanabilir** bir çıktı üretmesidir.

### 4. Ölü kod tespiti

Bu katman, ADR disiplininin doğrudan tamamlayıcısıdır: bir karar kaldırıldığında geride
kalan kullanılmayan export'ları, dosyaları ve bağımlılıkları yakalar.

```bash
# JS/TS ekosisteminde yaygın seçenekler
npx knip
npx ts-prune
```

Diğer dillerde eşdeğerleri vardır (Python: `vulture`, Go: `staticcheck` unused kuralları).

### 5. Güvenlik taraması

```bash
npm audit          # bağımlılık açıkları
pipx run reuse lint  # lisans başlığı uyumu (bkz. lisans.md)
```

Gizli anahtar taraması (`gitleaks` gibi) da bu katmana eklenebilir — commit'e sızmış bir
token'ı sonradan temizlemek, önlemekten çok daha pahalıdır.

## Testin rolü

Testler burada iki iş yapar; ikincisi sıklıkla gözden kaçar:

1. **Doğruluk kanıtı** — kodun beklendiği gibi çalıştığını gösterir.
2. **Karar temizliğinin zorlayıcısı** — bir ADR bir davranışı kaldırdıysa, o davranışın
   testi ya silinmeli ya kırmızıya düşmelidir. Eski davranışın testi hâlâ yeşilse, ya test
   yanlıştır ya kod hâlâ eski davranışı sürdürüyordur. Bu, `adr-rehberi.md`'deki
   "Kaldırılacaklar" listesinin otomatik doğrulayıcısıdır.

**Eşikler için test şarttır.** Bir ADR sayısal bir eşik tanımlıyorsa (`min 360 px` gibi),
o eşiğin testi olmalıdır — aksi halde eşik zamanla kodda sessizce kayar.

**AI ile üretilen kodda özel bir risk:** aynı model hem kodu hem testi yazarsa, ikisi de aynı
yanlış varsayımı paylaşabilir (ör. bir alan adını yanlış hatırlıyorsa hem kodda hem testte
aynı yanlış ismi kullanır). Bunu azaltmak için testleri yazdırırken gerçek şemayı/sözleşmeyi
ayrıca bağlama ver — test, kodun kendini onayladığı bir döngü değil, dış bir referansa karşı
karşılaştırma olmalıdır.

## Çapraz denetim

Statik analiz araçlarının bulunmadığı diller ve alanlar için (niş diller, DSL'ler, altyapı
tanımları), tek gerçekçi doğrulama yolu ayrı bir denetim turudur:

Kod üretildikten **sonra, ayrı bir oturumda**, dört sütunu tek tek sorgulayan bir denetim
iste — "her fonksiyonu Reliability / Security / Performance / Maintainability kriterlerine
karşı denetle, ihlalleri gerekçeleriyle listele". Bu, örtük bir "uydum" iddiasını, açık bir
bulgu listesine çevirir.

Ayrı oturum olması önemlidir: aynı bağlamda kendi kodunu denetleyen model, kendi
varsayımlarını tekrar eder.

## CI entegrasyonu

Yukarıdaki katmanların değeri, **çalıştırmayı hatırlamaya bağlı olmaktan çıkmalarıyla**
ortaya çıkar. Faz 0.7'de oluşturulan boş CI iskeletini burada doldur ve "pipeline yeşil
olmadan merge yok" kuralına bağla.

Minimum job seti:

| Job | Ne yapar | Başarısızsa |
|---|---|---|
| `tip-kontrol` | Derleyici / tip denetimi | Merge engellenir |
| `lint` | Linter + format | Merge engellenir |
| `test` | Test suite | Merge engellenir |
| `olu-kod` | Kullanılmayan export/dosya | Uyarı (başta), sonra engelleyici |
| `guvenlik` | Bağımlılık + lisans taraması | Merge engellenir |

Ölü kod job'unu başlangıçta uyarı seviyesinde tutmak makuldür — mevcut bir projeye
eklendiğinde ilk çalıştırmada çok sayıda bulgu üretebilir ve akışı kilitler.

## Kontrol listesi

- [ ] Tip katmanı en katı modda
- [ ] Linter kurulu ve hata-sınıfı kuralları etkin
- [ ] Statik analiz sayısal çıktı üretiyor
- [ ] Ölü kod tespiti kurulu
- [ ] Bağımlılık + lisans taraması kurulu
- [ ] Test altyapısı çalışıyor, eşiklerin testi var
- [ ] Tüm bunlar CI'da otomatik çalışıyor
- [ ] "Pipeline yeşil olmadan merge yok" kuralı etkin
- [ ] Komutlar CLAUDE.md'ye yazıldı (her oturumda bilinsin)