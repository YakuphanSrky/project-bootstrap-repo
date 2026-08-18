---
name: project-bootstrap
description: Yeni bir yazılım projesini sıfırdan, standart ve denetlenebilir bir iskeletle başlatır — depo kurulumu, lisans/SPDX disiplini, dizin şeması, CLAUDE.md üretimi, çekirdek ADR'ler ve kod kalitesi altyapısı. Kullanıcı "yeni proje başlatalım", "repo kuralım", "bu projeyi standarda oturtalım", "ADR yazalım", "CLAUDE.md hazırla", "proje iskeleti", "bootstrap" gibi ifadeler kullandığında MUTLAKA bu skill'i kullan. Ayrıca mevcut bir projeye ADR disiplini, lisans başlığı, kalite araçları veya karar-kod senkronizasyonu eklemek istendiğinde de kullan — kullanıcı "skill" veya "standart" kelimesini hiç kullanmasa bile.
---

<!-- SPDX-License-Identifier: MIT -->

# Proje Başlatma (Project Bootstrap)

Yeni bir projeyi, sonradan denetlenebilir ve sürdürülebilir olacak şekilde başlatmak için
aşamalı bir kurulum akışı. Amaç hız değil **izlenebilirlik**: her kararın nerede alındığı,
neyin neden değiştiği ve kodun hangi karara dayandığı sonradan bulunabilir olmalı.

## Temel ayrım — bu skill neyi taşır, proje neyi taşır

| Katman | Ne taşır | Nerede yaşar |
|---|---|---|
| **Bu skill** | Metodoloji: sıra, disiplin, şablonlar | Skill dizini (her projede aynı) |
| **CLAUDE.md** | Proje özeli: hangi stack, hangi lisans, hangi repo | Proje kökü (projeden projeye değişir) |
| **ADR'ler** | Kararların gerekçesi ve tarihçesi | `/docs/adr/` |

Bu ayrımı koru. Skill'e proje-özel değer yazma; CLAUDE.md'ye metodoloji kopyalama.

---

## Etkileşim kuralları — akışın tamamında geçerli

Bu kurallar kurulum deneyiminin kalitesini belirler; atlama.

### 1. Aşama aşama ilerle, toplu sorma

Tüm soruları bir anda sorma. **Tek soru sor → cevabı bekle → o adımı uygula → tek satır
onayla → sonraki adıma geç.** Kullanıcıyı aynı anda 5+ kararla karşı karşıya bırakmak,
her kararı yüzeysel vermesine yol açar; kurulumun değeri de buradan gelir.

### 2. Gerçek bir bedel yoksa sorma — uygula ve bilgilendir

Bir adımın faydası varsa ve bedeli/riski yoksa, onu soru olarak sunmak gereksiz sürtünmedir.

**Örnek 1 — sorma, uygula:**
Bağlam: Platform GitLab, branch koruma ücretsiz.
Doğru: "Main branch'i korumaya aldım (GitLab'da ücretsiz)." → devam et.
Yanlış: "Main branch'i korumak ister misiniz?" → gereksiz soru, tek cevabı var.

**Örnek 2 — sor, çünkü gerçek bedel var:**
Bağlam: Platform GitHub, repo private, kullanıcı Free planda.
Doğru: "Main branch koruması GitHub Free'de private repo'da yok — Pro gerekiyor
(aylık birkaç dolar). Korumasız devam etmek de geçerli, özellikle tek kişi commit
ediyorsanız. Tercihiniz?"

### 3. Legal kararları asla varsayma

Lisans her zaman sorulur. Bir OSS lisansını veya kurumsal politikayı kullanıcı adına seçme.

### 4. Dil: prose Türkçe, makine sınırı İngilizce

Üretilen dokümanların (CLAUDE.md, ADR gövdesi, issue/MR açıklaması) prose kısmı Türkçe
yazılır. İngilizce kalanlar: SPDX kimlikleri, dosya/dizin/branch adları, YAML anahtarları,
Conventional Commits tip token'ları (`feat:`, `fix:`), değişken adları.

---

## Kurulum akışı

Fazlar sıralıdır çünkü her biri bir öncekinin çıktısına bağımlıdır. Sırayı bozma:
ADR'den önce CLAUDE.md iskeleti gerekir (Claude Code her oturumda onu okur), kalite
araçlarından önce ADR gerekir (config'ler stack kararına bağlı).

### Faz 0 — Mekanik kurulum

Bu adımlar tartışmalı tercih değil, standart hijyendir; ADR gerektirmez.

**0.1 — VCS platformu.** Sor: GitHub mı, GitLab mı (self-hosted veya .com)? CI syntax,
branch koruma mekaniği ve terminoloji (MR vs PR) buna bağlı olduğu için bu ilk sorudur.
Platform farkları için `references/vcs-platformlari.md` oku.

**0.2 — Repo oluştur** (veya mevcut repo'yu doğrula).

**0.3 — Branch koruma.** Etkileşim kuralı 2'yi uygula: GitLab'da sessizce uygula ve
bilgilendir; GitHub + private repo'da gerçek maliyet olduğu için sor.

**0.4 — Lisans.** Her zaman sor:
- Mevcut bir OSS lisansı mı (MIT, Apache-2.0, GPL-3.0...)?
- Kurumsal/özel bir lisans politikası mı?

Özel politika ise ve GitHub/GitLab MCP bağlıysa, verilen depo bağlantısındaki politika
dosyasını **gerçekten oku** ve SPDX kimliğini doğrula. MCP bağlı değilse kullanıcıdan
SPDX kimliğini ve lisans metnini iste; ötesine geçme, doğrulama iddiasında bulunma.
Ayrıntı: `references/lisans.md`.

**0.5 — Dizin şeması.** `assets/dizin-semasi.md`'deki şemayı uygula. Boş dizin açma;
her dizin ihtiyaç doğduğunda oluşur. `/docs/adr/` istisnadır, baştan açılır.

**0.6 — `.notes/` + `.gitignore`.** Kullanıcının ekran görüntüsü, hata dökümü, ham not
gibi geçici materyali koyacağı, depoya girmeyen bir alan. `.gitignore`'a ekle.
Kullanıcıyı uyar: bu klasör yalnız o makinede yaşar, yedeklenmez.

**0.7 — CI iskeleti.** Yalnız dosya ve boş job yapısı; gerçek job'lar Faz 2'de dolar.

### Faz 0.5 — CLAUDE.md iskeleti

Claude Code her oturuma CLAUDE.md okuyarak başlar. ADR'lere geçmeden önce bu dosya var
olmalı, yoksa ilk ADR yanlış branch adıyla, yanlış dizine, yanlış commit formatıyla
yazılır.

`assets/CLAUDE.md.template` dosyasını oku ve **yalnız değişkenleri** doldurarak üret.
Şablonun sabit bölümlerini yeniden yazma veya yeniden düzenleme — tutarlılık modelin her
seferinde aynı kararı vermesinden değil, şablonun sabit olmasından gelir.

**Sorulacak değişkenler** (tek tek, Etkileşim Kuralı 1'e göre):

| Değişken | Neden sorulur |
|---|---|
| `{{PROJE_ADI}}`, `{{PROJE_TANIMI}}` | Kimlik |
| `{{VCS_PLATFORM}}`, `{{REPO_URL}}` | Faz 0'dan devralınır, tekrar sorma |
| `{{LISANS_SPDX}}`, `{{TELIF_SAHIBI}}` | Faz 0'dan devralınır, tekrar sorma |
| `{{TECH_STACK}}` | Belliyse; değilse "ADR-0001'de kararlaştırılacak" yaz |
| `{{VERITABANI}}` | Yer tutucu; detay Faz 1'de |
| `{{AUTH_SINIRI}}` | Yer tutucu; detay Faz 1'de |

**Sorulmayanlar** — şablonun sabit metninden doğrudan yazılır: dizin şeması, iş akışı
zinciri (issue → branch → commit → MR), commit formatı, AI atıf satırı, kalite standardı
referansı, ADR disiplini.

Kişisel bilgi (isim, iletişim) bu dosyaya **girmez** — o, kullanıcının global
`~/.claude/CLAUDE.md` dosyasına aittir. Kullanıcı proje CLAUDE.md'sine kişisel iletişim
bilgisi eklemek isterse, bunun ekip deposunda paylaşılacağını hatırlat.

### Faz 1 — Çekirdek ADR'ler

Numara bloğu rezerve et: `ADR-0001`–`ADR-0009` çekirdek, feature ADR'leri `ADR-0010`'dan
başlar. Numaralandırmanın kendisi "bu değişirse her şey sarsılır" uyarısını taşır.

Sırayla, her biri kapanmadan diğerine geçmeden:

1. **ADR-0001 — Tech stack / implementasyon ortamı**
2. **ADR-0002 — Veritabanı:** hangi DB, migration stratejisi, şema değişikliği nasıl izlenir
3. **ADR-0003 — Auth/Authz:** kimlik doğrulama nerede ve **gerçek yetki sınırı hangi
   katmanda**. Bunu ilk günden açıkça yaz; sonradan keşfedilen yetki sınırı, üzerine
   kurulmuş her kararı geçersizleştirir.
4. **ADR-0004 — Dizin şeması ve iş akışı zincirinin resmileştirilmesi**
5. **ADR-0005 — Kod kalitesi standardı** (varsayılan: ISO/IEC 5055:2021)

ADR yazımı, supersede zincirleri ve "Kaldırılacaklar" disiplini için
`references/adr-rehberi.md` oku. Şablon: `assets/ADR.md.template`.

Kapanmamış (Accepted olmamış) bir ADR üzerine implementasyona başlama.

### Faz 2 — Kalite altyapısı

Bu faz ADR'lerin **sonucunu uygular**; ADR'lerden önce kurulamaz çünkü her config bir
karara bağlıdır (TypeScript strict → stack kararı; migration job → DB kararı).

Kurulacaklar ve gerekçeleri için `references/kalite-araclari.md` oku. Özet:
tip katmanı (varsa strict mod), linter, statik analiz, ölü kod tespiti, test altyapısı,
ve bunların CI'da otomatik çalışması. Faz 0.7'deki boş CI iskeleti burada dolar.

### Faz 3 — CLAUDE.md güncellemesi

Çekirdek ADR'ler kapandıkça CLAUDE.md'nin ilgili bölümlerini (stack, dizin şeması, kalite
standardı) bu kararların sonucuyla doldur. CLAUDE.md yalnız **güncel durumu** anlatır;
"nasıl buraya gelindi" tarihçesi ADR'lerde yaşar. Bu ayrımı koru — aksi halde CLAUDE.md
her oturumda okunan şişkin bir changelog'a dönüşür.

### Faz 4 — Geliştirme başlar

Her feature ADR'i, hangi çekirdek ADR'lere dayandığını ve hangilerine dokunmadığını açıkça
belirtir. Bir kararı geçersiz kılıyorsa, `references/adr-rehberi.md`'deki supersede ve
"Kaldırılacaklar" disiplinini uygula.

---

## Karar-kod senkronizasyonu

Bu skill'in çözdüğü en önemli problem: **karar dokümanda değişir, kod eski hâliyle kalır.**
Zamanla proje, hangi kodun hangi karara dayandığı belirsiz bir yığına dönüşür.

Bunu üç mekanizma birlikte önler — üçü de kurulmalı, biri tek başına yetmez:

1. **Kaldırma listesi** — Bir ADR başka birini geçersiz kıldığında, silinmesi gereken
   alan/fonksiyon/dosya yollarını listeler ve bu liste bir issue'ya dönüşür.
2. **Greplenebilir geri-referans** — Kodda ilgili satırlar ADR'ye referans taşır
   (`// ADR-0008 D2` gibi). Bir karar geçersizleştiğinde etkilenen her yer aranabilir olur.
3. **Testler** — Kaldırılan bir davranışın testi hâlâ geçiyorsa, ya test yanlıştır ya kod
   hâlâ eski davranışı sürdürüyordur. Test, temizliğin otomatik kanıtıdır.

Ayrıntı ve periyodik denetim (drift audit) yöntemi: `references/adr-rehberi.md`.

---

## Mevcut projeye uygulama

Kullanıcı sıfırdan başlamıyorsa, tüm fazları baştan çalıştırma. Önce mevcut durumu tespit et
(hangi adımlar zaten var), eksikleri listele ve **hangisinden başlamak istediğini sor**.
Çalışan bir projeye tek seferde altı yeni disiplin dayatmak, hiçbirinin benimsenmemesiyle
sonuçlanır.

Tipik başlangıç noktası: CLAUDE.md yoksa Faz 0.5, varsa doğrudan Faz 1 (çekirdek ADR'ler
retroaktif olarak yazılır — "zaten alınmış ama yazılmamış kararları" kayda geçirmek).

---

## Referanslar

Gerektiğinde oku, hepsini önceden yükleme:

| Dosya | Ne zaman oku |
|---|---|
| `references/vcs-platformlari.md` | Faz 0.1–0.3: platform seçimi, branch koruma, CI farkları |
| `references/lisans.md` | Faz 0.4: SPDX, REUSE, MCP ile politika doğrulama |
| `references/adr-rehberi.md` | Faz 1 ve 4: ADR yazımı, supersede, kaldırma disiplini |
| `references/kalite-araclari.md` | Faz 2: linter, statik analiz, test, ölü kod, CI |

Şablonlar:

| Dosya | Kullanım |
|---|---|
| `assets/CLAUDE.md.template` | Faz 0.5 |
| `assets/ADR.md.template` | Faz 1 ve 4 |
| `assets/dizin-semasi.md` | Faz 0.5 |

---

## Bilinen sınırlar

Bunları kullanıcıya uygun anda söyle; sessizce varsayma:

- **Şablon drift'i:** Bu skill'in şablonu güncellenirse, daha önce oluşturulmuş projelerin
  CLAUDE.md'leri otomatik güncellenmez. Senkronizasyon manuel bir iştir.
- **`.notes/` yedeklenmez:** `.gitignore`'lu olduğu için yalnız o makinede yaşar.
- **Platform özellik matrisleri değişir:** `references/vcs-platformlari.md`'deki ücretsiz/
  ücretli ayrımları zamanla değişebilir. Kritik bir karar buna bağlıysa, platformun güncel
  dokümantasyonundan doğrula.