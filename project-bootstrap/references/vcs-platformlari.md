<!-- SPDX-License-Identifier: MIT -->

# VCS Platform Farkları

Platform seçimi metodolojiyi değiştirmez, **mekaniği** değiştirir. Bu dosya, aynı adımın
her platformda nasıl karşılık bulduğunu ve nerede gerçek maliyet farkı olduğunu anlatır.

> Aşağıdaki ücretsiz/ücretli ayrımları Ağustos 2026 itibarıyla geçerlidir. Kritik bir karar
> buna bağlıysa platformun kendi fiyatlandırma sayfasından doğrula.

## İçindekiler

1. [Terminoloji eşlemesi](#terminoloji-eşlemesi)
2. [Branch koruma](#branch-koruma)
3. [Zorunlu onay ve ücretsiz alternatifi](#zorunlu-onay-ve-ücretsiz-alternatifi)
4. [CI dosya konumu ve syntax](#ci-dosya-konumu-ve-syntax)
5. [Kurulum kontrol listesi](#kurulum-kontrol-listesi)

---

## Terminoloji eşlemesi

Üretilen tüm metinlerde (issue açıklaması, commit gövdesi, CLAUDE.md) platformun kendi
terminolojisini kullan. Karışık kullanım, dokümanın hangi platforma ait olduğunu belirsizleştirir.

| Kavram | GitHub | GitLab |
|---|---|---|
| Değişiklik önerisi | Pull Request (PR) | Merge Request (MR) |
| CI yapılandırması | `.github/workflows/*.yml` | `.gitlab-ci.yml` |
| CI çalıştırma birimi | workflow / job | pipeline / job |
| Kod sahipliği | `CODEOWNERS` | `CODEOWNERS` (Premium) |
| Grup yapısı | Organization | Group / Subgroup |

## Branch koruma

Amaç: `main` (veya varsayılan branch) üzerine doğrudan push, force-push ve silme işlemlerini
engellemek. Değişiklikler yalnız PR/MR üzerinden girer.

| Platform | Public repo | Private repo | Maliyet |
|---|---|---|---|
| GitLab (.com veya self-hosted) | ✅ Free | ✅ Free | Yok |
| GitHub | ✅ Free | ❌ Pro/Team gerekir | Aylık ücret |

**GitHub'ın kısıtı gerçektir ve sık gözden kaçar.** Resmi dokümantasyon, korumalı
branch'lerin GitHub Free'de yalnız public repo'larda kullanılabildiğini; private repo'da
Pro, Team veya Enterprise gerektiğini açıkça belirtir. Aynı kısıt "GitHub Free for
organizations" için de geçerlidir — organizasyon hesabı olması bunu değiştirmez.

**Uygulama farkı:**
- GitLab → sessizce uygula, tek satır bilgilendir. Gerçek bir ödünleşim yok.
- GitHub + private → sor. Seçenekler: (a) Pro'ya geç, (b) repo'yu public yap,
  (c) korumasız devam et. Tek kişilik projelerde (c) makul bir tercihtir; asıl fayda
  kazara force-push koruması olduğu için disiplinle telafi edilebilir.

## Zorunlu onay ve ücretsiz alternatifi

"Merge edilmeden önce N kişi onaylasın" kuralı her iki platformda da ücretli katmandadır.

| Platform | Ücretsiz katmanda | Ücretli katmanda |
|---|---|---|
| GitLab | Onay verilebilir ama **isteğe bağlıdır** — onaysız merge engellenmez | Premium: zorunlu onay kuralları |
| GitHub | Public repo'da PR review zorunlu kılınabilir | Pro/Team: private repo'da aynı |

**GitLab CE için ücretsiz çözüm:** Settings → Merge requests → "Pipelines must succeed"
işaretlenir; ardından CI'a, GitLab API'sinden mevcut MR'ın onay sayısını okuyan ve yetersizse
job'u başarısız yapan bir adım eklenir. Pipeline başarısız olduğunda merge engellendiği için
sonuç, ücretli "zorunlu onay" ile pratikte aynıdır.

Bu yaklaşımın maliyeti: bakım gerektiren bir script ve bir API token'ı. Kullanıcıya bunu
kurmak isteyip istemediğini sor; küçük ekiplerde genellikle gereksizdir.

## CI dosya konumu ve syntax

Faz 0.7'de yalnız **iskelet** oluşturulur — dosya var, gerçek job'lar boştur. Job'lar
Faz 2'de, stack kararı netleştikten sonra doldurulur.

**GitLab — `.gitlab-ci.yml`:**

```yaml
stages:
  - kalite
  - test

# Faz 2'de doldurulacak
kalite:
  stage: kalite
  script:
    - echo "Linter ve statik analiz buraya gelecek"

test:
  stage: test
  script:
    - echo "Test komutu buraya gelecek"
```

**GitHub — `.github/workflows/ci.yml`:**

```yaml
name: CI
on:
  pull_request:
  push:
    branches: [main]

jobs:
  kalite:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      # Faz 2'de doldurulacak

  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      # Faz 2'de doldurulacak
```

**Merge kapısına bağlama:** CI'ın "yeşil olmadan merge yok" kuralına bağlanması, kalite
araçlarının unutulmasını engelleyen tek mekanizmadır. GitHub'da branch koruma kuralında
"Require status checks to pass"; GitLab'da Settings → Merge requests → "Pipelines must
succeed" ile yapılır.

## Kurulum kontrol listesi

Platform seçildikten sonra:

- [ ] Repo oluşturuldu / doğrulandı
- [ ] Varsayılan branch adı netleşti (`main`)
- [ ] Branch koruma: uygulandı ya da kullanıcı bilinçli olarak reddetti
- [ ] CI dosyası doğru konumda, iskelet hâlinde
- [ ] "Pipeline başarılı olmadan merge yok" kuralı ayarlandı (CI dolduğunda etkinleşecek)
- [ ] Terminoloji (PR/MR) CLAUDE.md'ye doğru yazıldı