<!-- SPDX-License-Identifier: MIT -->

# project-bootstrap

Yeni bir yazılım projesini **standart ve denetlenebilir** bir iskeletle başlatan bir
[Claude Agent Skill](https://code.claude.com/docs/en/skills).

Amaç hız değil **izlenebilirlik**: her kararın nerede alındığı, neyin neden değiştiği ve
kodun hangi karara dayandığı altı ay sonra da bulunabilir olsun.

## Ne yapar

Kurulumu aşamalı olarak yürütür — tek seferde on soru sormaz, adım adım ilerler:

| Faz | İçerik |
|---|---|
| **0** | Depo, branch koruma, lisans/SPDX, dizin şeması, `.notes/`, CI iskeleti |
| **0.5** | `CLAUDE.md` üretimi (şablondan, yalnız değişkenler sorularak) |
| **1** | Çekirdek ADR'ler: stack, veritabanı, auth/authz, dizin şeması, kalite standardı |
| **2** | Kalite altyapısı: tip katmanı, linter, statik analiz, ölü kod, test, CI |
| **3** | `CLAUDE.md`'nin ADR sonuçlarıyla güncellenmesi |
| **4** | Geliştirme başlar — feature ADR'leri ve karar-kod senkronizasyonu |

### Çözdüğü asıl problem

Bir karar değişir, doküman güncellenir — ama **kod eski hâliyle kalır**. Zamanla hangi
kodun hangi karara dayandığı belirsizleşir ve proje iç içe geçmiş bir yığına dönüşür.

Skill bunu üç mekanizmayla önler:

1. **Kaldırılacaklar listesi** — bir karar geçersizleştiğinde silinmesi gerekenler
   listelenir ve issue'ya dönüşür; temizlik bitmeden merge edilmez.
2. **Greplenebilir geri-referans** — kodda `// ADR-0011 D4` etiketi; karar değişince
   etkilenen her yer tek komutla bulunur.
3. **Testler** — kaldırılan bir davranışın testi hâlâ geçiyorsa, temizlik eksiktir.

## Kurulum

### Claude Code

```bash
git clone https://github.com/YakuphanSrky/project-bootstrap-repo.git
cp -r project-bootstrap-repo/project-bootstrap ~/.claude/skills/
```

Proje bazlı kullanmak için `~/.claude/skills/` yerine `.claude/skills/` altına kopyalayın.

### Claude.ai

[Releases](../../releases) sayfasından `project-bootstrap.skill` dosyasını indirin,
Settings → Capabilities üzerinden yükleyin.

## Kullanım

Kurulduktan sonra otomatik tetiklenir. Örnek:

```
Yeni bir proje başlatacağım, iskeletini kuralım.
```

```
Bu projeye ADR disiplini ekleyelim, hiç yok.
```

Mevcut bir projeye uygularken skill önce ne var ne yok tespit eder, eksikleri listeler ve
nereden başlamak istediğinizi sorar — hepsini birden dayatmaz.

## Yapı

```
project-bootstrap/
├── SKILL.md                        # Akış, etkileşim kuralları, faz sırası
├── references/
│   ├── vcs-platformlari.md         # GitHub/GitLab farkları, branch koruma, CI
│   ├── lisans.md                   # SPDX, REUSE, MCP ile politika doğrulama
│   ├── adr-rehberi.md              # Supersede zinciri, kaldırma listesi, drift audit
│   └── kalite-araclari.md          # ISO/IEC 5055 eşlemesi, ücretsiz araçlar
└── assets/
    ├── CLAUDE.md.template
    ├── ADR.md.template
    └── dizin-semasi.md
```

Referanslar yalnız gerekli fazda okunur — hepsi baştan bağlama yüklenmez.

## Tasarım ilkeleri

- **Aşama aşama sor.** Tek soru → cevap → uygula → onayla → sonraki. Aynı anda beş karar
  dayatmak, her kararın yüzeysel verilmesine yol açar.
- **Gerçek bedel yoksa sorma.** Faydası olan ve maliyeti olmayan bir adımı soru olarak
  sunmak gereksiz sürtünmedir — uygula ve tek satır bilgilendir.
- **Legal kararları varsayma.** Lisans her zaman sorulur.
- **Prose Türkçe, makine sınırı İngilizce.** SPDX kimlikleri, branch adları, YAML
  anahtarları ve commit tip token'ları İngilizce kalır.
- **Metodoloji skill'de, proje özeli CLAUDE.md'de.** İkisini karıştırma.

## Araçlar

Skill'in önerdiği doğrulama katmanının tamamı ücretsizdir: tip denetimi, linter, statik
analiz (SonarQube Community), ölü kod tespiti (`knip`, `ts-prune`), bağımlılık taraması
(`npm audit`), lisans uyumu (`reuse lint`).

ISO/IEC 5055:2021 dört sütununu (Reliability, Security, Performance Efficiency,
Maintainability) pratikte karşılamayı hedefler; standardın resmî sertifikasyonunu iddia
etmez.

## Katkı

Issue ve PR açabilirsiniz. Skill'in kendisi de kendi disiplinine tabidir: davranışını
değiştiren bir PR, gerekçesini açıklamalıdır.

## Lisans

MIT — bkz. [LICENSE](LICENSE).
