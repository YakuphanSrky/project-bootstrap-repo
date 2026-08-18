<!-- SPDX-License-Identifier: MIT -->
<!-- Copyright (c) 2026 Yakuphan -->

# ADR-0001 — ADR disiplini

| Alan | Değer |
|---|---|
| **Durum** | Accepted |
| **Tarih** | 2026-08-18 |
| **Karar verenler** | Yakuphan Sarikaya |
| **İlgili issue** | — (bkz. Takipler) |
| **Dayandığı ADR'ler** | — (ilk ADR) |
| **Etkilediği ADR'ler** | — |

---

## Bağlam

Bu deponun tezi, `CLAUDE.md` §1'de yazılı: kalitenin varsayım değil **ölçüm** olduğu,
kararların kayıtlı ve kodun kararlara bağlı olduğu bir iskelet. Aynı bölüm "bu repo kendi
disiplinine tabidir" diyor.

Buna rağmen bugüne kadar **tek bir karar bile ADR olarak kayda geçmedi.** On karar
`.notes/baglam.md` K1–K10 arasında prose olarak duruyor; ikisi (kapsam ve skill'in kendi
doğrulaması) hâlâ açık. `CLAUDE.md` §5 "Accepted olmamış bir ADR üzerine implementasyona
başlanmaz" diyor — yani `docs/adr/` boş olduğu sürece skill üzerinde meşru biçimde
çalışılamaz. Kural, kendi kendini bloke etmiş durumda.

İkinci bir sorun daha var: ADR yazmaya başlamadan önce **ADR'lerin nasıl yazılacağı**
sabitlenmeli — format, dosya adı, durum döngüsü, numara blokları, supersede kuralı.

Bu kurallar `project-bootstrap/references/adr-rehberi.md` içinde zaten yazılı. Ama orada
skill'in **öğüdü** olarak, yani skill'in ürettiği başka projelere hitap ederek yazılmışlar;
bu depoyu bağlayan bir karar olarak değil. Öğüt ile bağlayıcı karar arasındaki fark, skill'in
teşhis ettiği farkın aynısıdır: **talimat ≠ doğrulama.** Kendi kuralımızı kendimize
uygulamadığımız sürece iddia sınanmamış kalır.

## Değerlendirilen seçenekler

### Seçenek A — ADR disiplinini meta-ADR olarak ADR-0001'e yaz
- **Artı:** Sonraki her ADR'nin dayanacağı format tek seferde sabitlenir; format tartışması
  bir kez yapılır.
- **Artı:** `adr-rehberi.md`'nin özgün tercihleri — `Suspended` durumu, `0001`–`0009` blok
  rezervasyonu, Kaldırılacaklar listesinin zorunluluğu — öğüt olmaktan çıkıp bu depoyu
  bağlayan karara dönüşür.
- **Artı:** Hiçbir şeye bağımlı değil; bağımlılık zincirinin gerçek başlangıcı burası.
- **Eksi:** İçerik kararı vermeyi (kapsam) bir tur geciktirir.

### Seçenek B — Doğrudan içerik ADR'siyle başla, disiplini `adr-rehberi.md`'de bırak
- **Artı:** Kapsam kararı bir tur önce kapanır.
- **Eksi:** Format kararlaştırılmadan yazılan ilk ADR, format sonradan netleşince ya yerinde
  revizyona ya supersede'e girer. Bu, deponun önlemeye çalıştığı **drift'in ta kendisidir** —
  hem de ilk günde, kendi belgemizde.
- **Eksi:** `adr-rehberi.md` başka projelere hitap ediyor; bu depoyu bağlayan merci belirsiz kalır.

### Seçenek C — ADR tutma kararını hiç kayda geçirme, `CLAUDE.md` §5 yeterli sayılsın
- **Artı:** Sıfır seremoni.
- **Eksi:** `CLAUDE.md` tanımı gereği yalnız **güncel durumu** anlatır, gerekçe taşımaz. Kural
  değiştiğinde "neden böyleydi" sorusunun cevabı hiçbir yerde olmaz.
- **Eksi:** `CLAUDE.md` §5 şu an zaten eksik — geri-referans etiket formatını ve dosya adı
  desenini tanımlamıyor. Boşluğu görünür kılan bir merci olmadan bu eksikler fark edilmiyor.

### Neden A seçildi

B'nin maliyeti sandığından büyük: format kararı olmadan yazılan bir ADR'yi sonradan düzeltmek,
`adr-rehberi.md`'nin kendi diliyle ya "yerinde revizyon" ya "supersede zinciri" gerektirir.
İlk ADR'yi supersede etmekle işe başlamak, disiplinin kendisine olan güveni baştan zedeler.

C ise gerçek bir boşluğu görmezden geliyor: `CLAUDE.md` güncel durumu, `adr-rehberi.md` başka
projeleri kapsıyor. Bu deponun kendi ADR kurallarını bağlayan üçüncü bir merci yok — ADR-0001
o merci.

A'nın tek eksisi (kapsam kararının bir tur gecikmesi) gerçek bir maliyet doğurmuyor: kapsam
kararı yalnız skill testinden önce kapanmak zorunda (`CLAUDE.md` §8), ADR-0002 olarak yazılınca
bu şart fazlasıyla karşılanıyor.

---

## Kararlar

**D1.** Bu deponun mimari kararları `docs/adr/` altında ADR olarak kayda geçer. Accepted
olmamış bir ADR üzerine implementasyona başlanmaz.

**D2.** ADR formatının kaynağı `project-bootstrap/assets/ADR.md.template` dosyasıdır. Bu
depodaki ADR'ler o şablondan üretilir. Şablon ile ADR'ler ayrışırsa **şablon kaynaktır**;
formatı değiştirmek isteyen önce şablonu değiştirir.

**D3.** ADR dosya adı deseni: `docs/adr/ADR-NNNN-{kısa-ingilizce-kebab}.md`. Dosya adı makine
sınırıdır, bu yüzden İngilizce; belgenin başlığı ve gövdesi Türkçe kalır.

**D4.** Numara blokları rezerve edilir: `ADR-0001`–`ADR-0009` çekirdek, `ADR-0010` ve sonrası
feature. Numaranın kendisi kararın ağırlığını taşıyan bir sinyaldir.

**D5.** Durum döngüsü: `Proposed → Accepted → (Superseded by ADR-XXXX | Deprecated | Suspended)`.
`Suspended`, `Superseded`den ayrı tutulur: "karar yanlıştı" ile "karar doğruydu ama uygulanacak
yüzey şu an yok" aynı şey değildir ve ikincisi geri gelebilir.

**D6.** Bir kararı değiştirme kuralı hedefin durumuna bağlıdır. Hedef `Proposed` ise belge
doğrudan düzenlenir, zincir kurulmaz. Hedef `Accepted` ise yeni ADR yazılır, eskisi silinmez,
başına `Superseded by ADR-XXXX` notu düşülür. Kısmi supersede tercih edilir ve madde
numarasıyla yazılır (ör. "ADR-0013, ADR-0008 **D2**'nin yerine geçer; D7 yürürlükte kalır").

**D7.** Başka bir kararı geçersiz kılan her ADR, `Kaldırılacaklar` bölümünü doldurur. Bu liste
bir issue'ya dönüşür ve temizlik tamamlanmadan ilgili PR merge edilmez.

**D8.** Karar-kod geri-referans etiketi: kodda `// ADR-NNNN DN`, markdown'da
`<!-- ADR-NNNN DN -->`. Etiket madde numarasını içerir; ADR numarası tek başına yetmez, çünkü
kısmi supersede madde düzeyinde işler (D6).

**D9.** Bir çekirdek ADR (`0001`–`0009`) değiştiğinde bu sıradan bir supersede değildir; drift
audit tetikler (`project-bootstrap/references/adr-rehberi.md` §Drift audit).

---

## Kapsam dışı

- **Skill'in ürettiği projelerin ADR disiplini.** Bu ADR yalnız **bu depoyu** bağlar. Başka
  projelere ne öğütlendiğini `project-bootstrap/SKILL.md` Faz 1 ve `references/adr-rehberi.md`
  tanımlar. İkisi tasarım gereği örtüşür ama **ayrı mercilerdir** — biri değişince diğeri
  otomatik değişmez. Bu ayrım korunmalı, yoksa skill'e proje-özel değer sızar.
- **Çekirdek blokta hangi kararların yer alacağı.** Blok sınırı burada (D4), içeriği
  ADR-0002 ve sonrasında.
- **ADR'lerin CI'da otomatik denetlenmesi.** Aşağıdaki doğrulama adımları şu an manueldir;
  otomatikleştirilmesi ADR-0007'nin (doğrulama standardı) konusudur.
- **`.notes/baglam.md`'nin akıbeti.** Gerekçeler ADR'lere taşındıkça o belge tarihsel kayıt
  olarak kalır; taşınma sırası ve belgenin sonu ayrı bir konudur.

---

## Sonuçlar

**Olumlu:**
- Kalan altı çekirdek ADR tek bir formata yazılır; format tartışması bir kez yapılır.
- `adr-rehberi.md`'nin özgün tercihleri bu depoda da bağlayıcı olur — "bu repo kendi
  disiplinine tabidir" iddiası ilk kez somut bir karşılık bulur.
- İki boşluk kapanır: **geri-referans etiket formatı** (D8) ve **dosya adı deseni** (D3). İkisi
  de şu an hiçbir yerde tanımlı değil, üstelik `adr-rehberi.md` etiket formatının CLAUDE.md'ye
  yazılmasını açıkça istiyor.
- `docs/adr/` boş olmaktan çıkar; §5'in kendi kendini bloke eden durumu çözülür.

**Ödünler (bilinçli kabul edilenler):**
- **Kapsam kararı (AS1) bir tur gecikir**, ADR-0002'ye kayar. Skill testi zaten ondan sonra
  geldiği için gerçek bir gecikme maliyeti doğmuyor.
- **D2, şablonu tek kaynak yapar** — yani ADR formatını iyileştirmek isteyen önce
  `assets/ADR.md.template`'i değiştirmek zorunda. Bu kasıtlı bir sürtünmedir: şablon
  sabitliğinin bedeli, formatın anlık olarak esnetilememesi.
- **Dokuz karar maddesi ilk ADR için hacimli.** Ayrı ADR'lere bölünebilirdi; bölünmedi çünkü
  hepsi tek bir mekaniğin parçaları ve ayrıştırmak çapraz referans yükünü karar sayısından
  daha hızlı büyütürdü.
- **Bootstrap paradoksu:** ADR-0001, kendi tanımladığı formata uymak zorunda. Kaçınılmaz;
  aşağıdaki doğrulama bölümünde açıkça sınanıyor.

---

## Kaldırılacaklar

Bu ADR hiçbir kararı geçersiz kılmıyor — ilk ADR. Bölüm bilinçli olarak boştur.

---

## Takipler

- [x] `CLAUDE.md` §5, D3 ve D8 ile güncellendi (PR #2).
- [ ] **ADR-0002 — Kapsam ve problem sahipliği.** `.notes/baglam.md` AS1 burada kapanır.
- [ ] **`SKILL.md` Faz 1 kod projesi varsayıyor.** Önerdiği çekirdek ADR seti stack / veritabanı
      / auth / dizin / kalite standardı — bu deponun hiçbirinde karşılığı yok. Skill'i kendine
      uygularken çıkan dogfooding bulgusu; ayrı issue.
- [ ] **`CLAUDE.md` §4'te AI atıf satırı eksik.** `assets/CLAUDE.md.template` §7 her AI commit'inde
      bu satırı zorunlu kılıyor, deponun kendi CLAUDE.md'si istemiyorlar arasında saymıyor. Ayrı issue.
- [ ] **İş akışı sapması kayda geçti.** Bu ADR issue'suz yazıldı: GitHub token'ında
      `issues: write` yetkisi yok (403), dolayısıyla `CLAUDE.md` §4'ün gerektirdiği
      `{issue-no}-{kebab}` branch adı ve `Closes #N` satırı kurulamadı. Yetki eklenince akışa
      dönülecek ve bu ADR'nin issue alanı doldurulacak.
- [ ] **ADR indeksi gerekli mi?** Drift audit'in 1. adımı "Accepted kararları listele" diyor;
      şimdilik `ls docs/adr/` yetiyor. Sayı artarsa `docs/adr/README.md` düşünülür.

---

## Doğrulama

Bu ADR'nin uygulandığı, sonraki her ADR'nin aşağıdakileri geçmesiyle doğrulanır:

```bash
# D3 — dosya adı deseni
ls docs/adr/ | grep -vE '^ADR-[0-9]{4}-[a-z0-9-]+\.md$'   # çıktı boş olmalı

# D2 — şablonun zorunlu bölümleri korunmuş
for f in docs/adr/ADR-*.md; do
  for b in "## Bağlam" "## Değerlendirilen seçenekler" "## Kararlar" "## Kapsam dışı" \
           "## Sonuçlar" "## Kaldırılacaklar" "## Takipler" "## Doğrulama"; do
    grep -q "$b" "$f" || echo "EKSİK: $f → $b"
  done
done

# Şablon yer tutucusu sızmamış. Desen dar tutuldu (çift süslü parantez + büyük harf):
# düz bir '\{\{' araması bu bloğun kendisini yakalar ve her zaman yanlış pozitif verir.
grep -lE '\{\{[A-Z_]+\}\}' docs/adr/*.md    # çıktı boş olmalı

# D8 — geri-referans aranabilir
grep -rn "ADR-0001" CLAUDE.md docs/
```

**Bootstrap kontrolü:** ADR-0001'in kendisi D2 ve D3'e uyar — dosya adı
`docs/adr/ADR-0001-adr-discipline.md`, bölümleri şablondan gelir.

**Kendine referans tuzağı:** Bir denetimin, denetlediği dosyanın *içinde* yaşaması onu
kırılgan yapar — kontrol kendi metnini eşleştirebilir. Yukarıdaki yer tutucu araması bu ADR
yazılırken tam olarak bunu yaptı ve iki kez daraltılmak zorunda kaldı. Denetimin doğru yeri
belgenin içi değil CI'dır; bu, ADR-0007'nin gerekçesine eklenecek somut bir kanıttır.

**Bilinen sınır:** Bu adımlar şu an manuel çalıştırılır; `adr-rehberi.md`'nin kendi ilkesi
olan "ölçülemeyen eşik test edilemez" tam karşılanmış değil. Otomatikleştirme ADR-0007'ye
bırakıldı ve orada kapanmadan bu ADR'nin doğrulaması eksik sayılır.
