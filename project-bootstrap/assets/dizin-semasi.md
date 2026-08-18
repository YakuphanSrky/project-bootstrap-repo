<!-- SPDX-License-Identifier: MIT -->

# Varsayılan Dizin Şeması

Bu şema bir başlangıç noktasıdır, dogma değil. Stack'e göre uyarla — ama uyarlamayı
**ADR-0004'te kayda geçir**, çünkü dizin yapısı sonradan değiştirmesi pahalı bir karardır.

## Kök yapı

```
<proje>/
├── .notes/                  # Kişisel çalışma alanı — .gitignore'da
├── docs/
│   └── adr/                 # Mimari Karar Kayıtları
│       ├── ADR-0001-tech-stack.md
│       └── ...
├── src/                     # Uygulama kaynağı
├── tests/                   # Testler
├── LICENSES/                # Lisans tam metinleri (REUSE)
├── .gitignore
├── CLAUDE.md
├── LICENSE
└── README.md
```

Platforma göre ek olarak: `.github/workflows/` (GitHub) veya `.gitlab-ci.yml` (GitLab).

## `src/` iç yapısı

Katman adlarını stack'in kendi geleneğine göre seç; önemli olan **sorumlulukların
ayrılması** ve bunun tutarlı kalmasıdır.

Genel amaçlı bir başlangıç:

```
src/
├── ui/              # Arayüz bileşenleri
├── domain/          # İş mantığı — dış dünyaya bağımlı olmayan çekirdek
├── data/            # Veri modelleri, şema, migration
├── services/        # Dış sistem entegrasyonları
├── api/             # Yayınlanan arayüz (varsa)
└── config/          # Yapılandırma
```

## Kurallar

**Boş dizin açma.** Her dizin ihtiyaç doğduğunda oluşur. İstisna: `docs/adr/` baştan açılır,
çünkü ilk ADR kurulumun parçasıdır.

**Bir dizinin ne barındıracağı ADR'de tanımlıdır.** "Bu dosya nereye gider?" sorusunun
cevabı tartışmaya açık olmamalıdır; belirsizse ADR eksiktir.

**Testler kaynağı yansıtır.** `src/domain/scale.ts` → `tests/domain/scale.test.ts`.
Bu eşleme, bir modül kaldırıldığında testinin de kaldırılması gerektiğini görünür kılar.

## `.gitignore` başlangıcı

```gitignore
# Kişisel çalışma alanı — ekran görüntüleri, ham notlar, geçici dökümler
.notes/

# Bağımlılıklar ve derleme çıktıları
node_modules/
dist/
build/

# Ortam değişkenleri — asla depoya girmez
.env
.env.local

# Editör ve işletim sistemi
.DS_Store
.idea/
.vscode/*
!.vscode/extensions.json
```

`.notes/` hakkında kullanıcıyı bir kez uyar: bu klasör depoya girmediği için yalnız o
makinede yaşar ve yedeklenmez. Kaybolmaması gereken içerik buraya konmamalıdır.