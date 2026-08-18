<!-- SPDX-License-Identifier: MIT -->
<!-- Copyright (c) 2026 Yakuphan -->

# ADR-0002 — Kapsam ve problem sahipliği

| Alan | Değer |
|---|---|
| **Durum** | Proposed |
| **Tarih** | 2026-08-18 |
| **Karar verenler** | Yakuphan Sarikaya |
| **İlgili issue** | — (token'da `issues: write` yok; bkz. ADR-0001 Takipler) |
| **Dayandığı ADR'ler** | ADR-0001 (D2 format, D3 dosya adı, D4 numara bloğu) |
| **Etkilediği ADR'ler** | — |

---

## Bağlam

`.notes/baglam.md` §1, skill'in doğduğu ihtiyacı üç yüzle tarif ediyor:

1. **Doğrulanamayan kalite** — "şu standarda uy" bir talimattır; uyduğunu görmek ayrı bir
   doğrulama işidir.
2. **Karar-kod kopukluğu (drift)** — karar dokümanda değişir, kod eski hâlini sürdürür.
3. **Her projede sıfırdan kurulum** — aynı disiplin her projede elle yeniden kurulur.

Artefakt şu an bu üçünü tek skill'de taşıyor ve adını **en küçüğünden** alıyor:
`project-bootstrap`, yani proje başına bir kez çalışan kurulum yüzünden. Kalıcı değer ise
ADR disiplininde — o, proje boyunca sürekli gerekiyor.

Bunun somut bir bedeli var. Bir skill'in `description` alanı tetiklemeyi belirler; tek alan
hem "yeni proje kuruyorum" hem "şu kararı supersede ediyorum" bağlamını temsil etmek zorunda
kalıyor. İki bağlam arasında ortak kelime az, dolayısıyla tetikleme bulanıklaşıyor.

Mevcut yapı da bunu işaret ediyor: `references/adr-rehberi.md` ve
`references/kalite-araclari.md` kendi başlarına ayakta duran gövdeler — bootstrap'in
referansı olmaktan çıkmışlar.

Bu soru `.notes/baglam.md` §5'te **AS1** olarak açık bırakılmıştı ve üç alt sorusu vardı:
bölünecek mi; bölünürse ortak referansları kim taşıyacak; nasıl paketlenecek.

## Değerlendirilen seçenekler

### Seçenek A — Yaşam döngüsü ekseninde üçe böl
- **Artı:** Her skill'in `description`'ı tek bir yaşam döngüsü anını temsil eder; tetikleme keskinleşir.
- **Artı:** `adr-discipline` ömür boyu değer taşır ve bootstrap'in ölü ağırlığından kurtulur.
- **Artı:** Ayrım ekseni konu değil **yaşam döngüsü** — hangi skill'in ne zaman çalıştığı belirsiz kalmaz.
- **Eksi:** Üç bakım yüzü, üç `description`, üç paket.
- **Eksi:** Bootstrap tek başına Faz 1–2'de eksik kalır; devir gerektirir.

### Seçenek B — İkiye böl (bootstrap + adr-discipline)
- **Artı:** İki bakım yüzü; kalite araçları asıl Faz 2'de danışıldığı için kurulumun içinde kalır.
- **Eksi:** Kalite katmanının bakımı (yeni linter eklemek, eşik değiştirmek) kurulum sonrası da
  sürüyor; bootstrap'in içinde kalınca bu iş için tetiklenecek bir yüzey olmuyor.

### Seçenek C — Tek skill kalsın
- **Artı:** Tek bakım yüzü, sıfır göç maliyeti.
- **Eksi:** Tetikleme bulanıklığı çözülmez; Faz 4'ten sonra bootstrap ölü ağırlık olarak
  bağlamda kalmaya devam eder.

### Seçenek D — Daralt: yalnız ADR disiplini
- **Artı:** Kalıcı değerin ADR'de olduğu teşhisinin en keskin sonucu.
- **Eksi:** "Her projede sıfırdan kurulum" ihtiyacı — üç yüzden biri — karşılanmadan kalır.
  Mevcut bootstrap ve kalite içeriği çöpe gider.

### Neden A seçildi

C'nin tek gerçek avantajı göç maliyetinin sıfır olması; ama bu maliyet bir kez ödenir,
tetikleme bulanıklığı ise her kullanımda ödenir.

D, üç yüzden ikisini terk ediyor. Teşhis doğru — kalıcı değer ADR'de — ama teşhisin sonucu
diğer ihtiyaçları yok saymak değil, onları kendi yaşam döngülerine ayırmak.

B ile A arasındaki fark tek kalemde toplanıyor: kalite katmanının bakımı kurulumdan sonra da
sürüyor mu? Sürüyor — yeni bir linter eklemek, bir eşiği sertleştirmek, CI kapısı değiştirmek
kurulum işi değil. Bu iş için tetiklenecek bir yüzey gerekiyor, dolayısıyla üçüncü skill
gerekçesini karşılıyor.

---

## Kararlar

**D1.** Artefakt üç skill'e bölünür. Ayrım ekseni **konu değil yaşam döngüsüdür**: bir skill'in
ne zaman ve hangi sıklıkta çalıştığı, onun sınırını belirler.

**D2.** Problem sahipliği tek tek atanır; her yüzün tek sahibi vardır:

| Skill | Sahiplendiği problem | Ne zaman çalışır | Sıklık |
|---|---|---|---|
| `project-bootstrap` | Her projede sıfırdan kurulum | Proje ilk kurulurken | Proje başına bir kez |
| `adr-discipline` | Kayıtlı kararlar + karar-kod bağı | Karar alınırken, değiştirilirken, denetlenirken | Sürekli |
| `quality-gates` | Doğrulanabilir kalite | Doğrulama katmanı kurulurken ve bakımında | Ara sıra |

**D3.** Üçü tek depoda yaşar ve tek sürüm çizgisinde birlikte sürülür. Ayrı depolara bölmek,
aralarındaki devir ilişkisini sürüm uyumu sorununa dönüştürürdü.

**D4.** **Kaynakta kopya yoktur.** Çakışan her dosyanın tek sahibi vardır:

| Dosya | Sahibi |
|---|---|
| `adr-rehberi.md` | `adr-discipline` |
| `ADR.md.template` | `adr-discipline` |
| `kalite-araclari.md` | `quality-gates` |
| `vcs-platformlari.md`, `lisans.md`, `CLAUDE.md.template`, `dizin-semasi.md` | `project-bootstrap` |

**D5.** `project-bootstrap` Faz 1'de `adr-discipline`'a, Faz 2'de `quality-gates`'e **devreder**
ve bu fazlara ait referansları kendi dizininde taşımaz. Devir cümlesi SKILL.md'de açıkça yazılır.

**D6.** Her skill'in `description` alanı yalnız kendi yaşam döngüsü anını temsil eder. Bir
skill'in description'ı başka bir skill'in tetikleme bağlamını içermez.

**D7.** Bu ADR yalnız **kaynakta tek kopya** ilkesini bağlar. Paketleme mekanizması — üçü tek
plugin olarak mı, ayrı `.skill` paketleri olarak mı dağıtılacak ve paket üretimi sırasında
mekanik kopyalama yapılıp yapılmayacağı — `ADR-0003`'ün konusudur.

---

## Kapsam dışı

- **Dizin yapısı ve paketleme.** Depoda üç skill'in nasıl yerleşeceği, release'in ne üreteceği:
  `ADR-0003`.
- **Skill içeriklerinin yeniden yazımı.** Bu ADR sınırları çizer, metinleri değil.
- **Etkileşim modeli.** Üç skill'in de uyacağı sorgulama disiplini `ADR-0004`'ün konusu.
- **Skill adlarının kesinleşmesi.** `adr-discipline` ve `quality-gates` çalışma adıdır;
  değişirlerse bu ADR'nin D2 tablosu yerinde revize edilir (henüz Proposed ise) veya kısmi
  supersede edilir.

---

## Sonuçlar

**Olumlu:**
- Her `description` tek bağlamı temsil eder; tetikleme keskinleşir.
- `adr-discipline` proje ömrü boyunca değer taşır; bootstrap Faz 4'ten sonra bağlamdan çekilir.
- Çakışan dosyaların sahibi netleşir; "hangi kopya güncel" sorusu doğmaz.
- `.notes/baglam.md` AS1 kapanır.

**Ödünler (bilinçli kabul edilenler):**
- **Üç bakım yüzü.** Bir etkileşim kuralı değişirse üç SKILL.md'de güncellenmesi gerekebilir.
  Bu, `ADR-0004`'ün çözmesi gereken bir sorun olarak kayda geçti.
- **Bootstrap tek başına eksiktir.** Faz 1 ve Faz 2'de devir gerektirdiği için, yalnız
  bootstrap kuran bir kullanıcı o fazlarda referanssız kalır. Devir cümlesi bunu görünür
  kılar ama ortadan kaldırmaz.
- **Göç maliyeti gerçektir.** Mevcut `SKILL.md` bölünecek, `release.yml` yeniden yazılacak,
  README ve CLAUDE.md güncellenecek. Aşağıdaki Kaldırılacaklar listesi bu işi sayıyor.
- **Claude.ai tarafında kurulum adımı artar** — üç ayrı paket. Hafifletmesi `ADR-0003`'e ait.

---

## Kaldırılacaklar

Bu ADR bir ADR'yi supersede etmiyor (öncesinde kayıtlı karar yoktu), ama mevcut artefaktı
yeniden bölüyor. `ADR-0001` D7'nin ruhu gereği taşınacak ve silinecekler burada sayılır;
liste bitmeden bölme işi tamamlanmış sayılmaz.

- [ ] `project-bootstrap/references/adr-rehberi.md` → `adr-discipline`'a taşınır, bootstrap'ten silinir
- [ ] `project-bootstrap/assets/ADR.md.template` → `adr-discipline`'a taşınır, bootstrap'ten silinir
- [ ] `project-bootstrap/references/kalite-araclari.md` → `quality-gates`'e taşınır, bootstrap'ten silinir
- [ ] `project-bootstrap/SKILL.md` Faz 1 gövdesi → `adr-discipline`'a; yerine devir cümlesi (D5)
- [ ] `project-bootstrap/SKILL.md` Faz 2 gövdesi → `quality-gates`'e; yerine devir cümlesi (D5)
- [ ] `project-bootstrap/SKILL.md` `description` alanı daraltılır (D6)
- [ ] `project-bootstrap/SKILL.md` §Referanslar ve §Şablonlar tabloları yeni sahipliğe göre düzeltilir
- [ ] `.github/workflows/release.yml` tek skill paketliyor — üçe göre yeniden yazılır (mekanizma: ADR-0003)
- [ ] `README.md` §Yapı ve §Kurulum blokları üç skill'e göre güncellenir
- [ ] `CLAUDE.md` §2 (artefakt) ve §6 (dizin yapısı) güncellenir

---

## Takipler

- [ ] **`ADR-0003`'ün başlığı genişletilmeli.** Çekirdek haritada dağıtım/paketleme (baglam K8)
      hiçbir ADR'ye bağlı değil. D7 paketlemeyi 0003'e havale ettiğine göre 0003 "Artefakt
      biçimi, katman yapısı **ve dağıtım**" olmalı; `CLAUDE.md` §8 tablosu buna göre düzeltilir.
- [ ] **Skill adları kesinleşecek** — `adr-discipline`, `quality-gates`. Dizin adı makine
      sınırı olduğu için İngilizce kalması dil politikasına uygun (`ADR-0005`).
- [ ] **Ortak etkileşim kuralları üç yerde tekrarlanacak.** `ADR-0004` bunu ya tek sahibe
      bağlamalı ya tekrarı bilinçli kabul etmeli.
- [ ] **Skill testi bölme sonrasına kalır** (`CLAUDE.md` §8/4) — bölünecek bir yapıyı test
      etmemek için.

---

## Doğrulama

```bash
# D4 — çakışan dosya tek dizinde
for f in adr-rehberi.md kalite-araclari.md ADR.md.template; do
  n=$(find . -path ./.git -prune -o -name "$f" -print | wc -l | tr -d ' ')
  [ "$n" = "1" ] || echo "KOPYA VAR: $f ($n adet)"
done

# D5 — bootstrap devir cümlesini taşıyor mu
grep -q 'adr-discipline'  */project-bootstrap/SKILL.md || echo "EKSİK: Faz 1 devri"
grep -q 'quality-gates'   */project-bootstrap/SKILL.md || echo "EKSİK: Faz 2 devri"

# D6 — her description tek bağlamı anlatıyor mu (manuel okuma)
grep -h '^description:' */*/SKILL.md
```

**Bilinen sınır:** D6'nın doğrulaması otomatikleştirilemez — bir `description`'ın tek bağlamı
temsil edip etmediği okumayla anlaşılır. Tetiklemenin gerçekten keskinleştiği ancak
`CLAUDE.md` §8/4'teki testle görülür; o test yapılmadan bu ADR'nin doğrulaması eksik sayılır.
