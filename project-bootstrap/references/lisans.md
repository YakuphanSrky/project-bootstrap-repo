<!-- SPDX-License-Identifier: MIT -->

# Lisans ve Telif Disiplini

Lisans, bu akıştaki tek **legal** karardır. Diğer adımların çoğunda makul bir varsayılan
önerilebilir; burada öneremezsin. Yanlış lisans, projenin sonradan kullanılabilirliğini
doğrudan kısıtlar ve geri almak (katkı sağlayanların izni gerektiği için) zordur.

## İçindekiler

1. [Karar akışı](#karar-akışı)
2. [MCP ile politika doğrulama](#mcp-ile-politika-doğrulama)
3. [SPDX başlıkları](#spdx-başlıkları)
4. [REUSE uyumu](#reuse-uyumu)
5. [Kontrol listesi](#kontrol-listesi)

---

## Karar akışı

Kullanıcıya tek soru sor: **mevcut bir açık kaynak lisansı mı, yoksa kurumsal/özel bir
lisans politikası mı?**

### A) Mevcut bir OSS lisansı

Kullanıcı zaten biliyorsa doğrudan uygula. Bilmiyorsa seçenekleri **nötr biçimde** özetle —
hangisinin "doğru" olduğunu söyleme, çünkü bu projenin amacına bağlıdır:

| SPDX | Kısaca |
|---|---|
| `MIT` | En izin verici, en kısa. Atıf yeterli. |
| `Apache-2.0` | İzin verici + açık patent hükmü içerir. |
| `BSD-3-Clause` | MIT'e yakın, ek olarak isim kullanımını kısıtlar. |
| `GPL-3.0-only` | Türev çalışmaların da aynı lisansla dağıtılmasını gerektirir. |
| `AGPL-3.0-only` | GPL + ağ üzerinden sunulan hizmetleri de kapsar. |
| `LicenseRef-<isim>` | Standart olmayan/özel lisanslar için SPDX gösterimi. |

Karar veremiyorsa: hukuki tavsiye verme. "Bu bir hukuki karar; kurumunuzda bir politika
varsa ona bakmak veya danışmak en sağlıklısı" de ve akışı bloklamadan devam et
(lisans dosyası eklenene kadar `LICENSE` yer tutucusu bırakılabilir).

### B) Kurumsal / özel politika

Kullanıcıdan iki şey iste:
1. **SPDX kimliği** (ör. `LicenseRef-Firma-Proprietary`)
2. **Politika kaynağı** — depo bağlantısı, dosya yolu veya lisans metni

Sonra MCP durumuna göre davran (aşağıdaki bölüm).

## MCP ile politika doğrulama

**MCP bağlıysa** (GitHub/GitLab connector mevcutsa): verilen depo bağlantısındaki politika
dosyasını gerçekten oku. SPDX kimliğini, başlık formatını ve varsa footer/atıf şablonunu
kaynaktan al. Bu, elle aktarımda oluşan sapmayı önler.

**MCP bağlı değilse:** kullanıcıdan SPDX kimliğini ve gerekiyorsa lisans metnini yapıştırmasını
iste. Aldığın bilgiyi uygula, **ötesine geçme** — politika deposunu tahmin etme, lisans
metnini hafızadan üretme, "muhtemelen şöyledir" deme. Doğrulanmamış bir legal metin
üretmek, hiç üretmemekten kötüdür.

Kullanıcıya bir kez, kısaca bildir: MCP bağlanırsa politika dosyası doğrudan okunabilir ve
bu adım daha güvenilir olur. Bağlamak istemezse ısrar etme.

## SPDX başlıkları

Her kaynak dosyanın başına makine-okunur lisans bilgisi ekle. Bu, lisans denetim araçlarının
(ve insanların) dosya bazında lisansı görebilmesini sağlar.

**Örnek 1 — C-tarzı yorum (js, ts, java, go, c, css):**
```
// SPDX-License-Identifier: MIT
// Copyright (c) 2026 Örnek Şirket A.Ş.
```

**Örnek 2 — Kare-işaretli yorum (python, ruby, shell, yaml):**
```
# SPDX-License-Identifier: Apache-2.0
# Copyright (c) 2026 Örnek Şirket A.Ş.
```

**Örnek 3 — Markdown ve HTML:**
```
<!-- SPDX-License-Identifier: LicenseRef-Firma-Proprietary -->
<!-- Copyright (c) 2026 Örnek Şirket A.Ş. -->
```

Markdown dosyaları da dahildir — dokümantasyon da telif hakkına tabidir ve çoğu politika
bunu açıkça kapsar.

## REUSE uyumu

REUSE, lisans bilgisinin makine tarafından doğrulanabilir olmasını sağlayan bir spesifikasyondur.
Gereksinimleri:

1. Her dosyada SPDX başlığı (yukarıdaki gibi)
2. Kullanılan her lisansın tam metni `LICENSES/` dizininde (`LICENSES/MIT.txt` gibi)
3. Başlık konulamayan dosyalar (ikili dosyalar, görseller) için `.license` yan dosyası
   veya `REUSE.toml` girdisi

Doğrulama ücretsizdir ve CI'a eklenebilir:
```bash
pipx run reuse lint
```

Bu adımı Faz 2'de CI'a bağlamak, lisans başlığı unutulan dosyaların merge edilmesini
engeller — disiplinin otomatik hâle geldiği yer burasıdır.

## Kontrol listesi

- [ ] Lisans kullanıcıya soruldu, varsayılmadı
- [ ] SPDX kimliği netleşti
- [ ] Özel politika ise: MCP ile doğrulandı ya da kullanıcıdan alındı (tahmin edilmedi)
- [ ] Kök dizinde `LICENSE` dosyası var
- [ ] `LICENSES/` dizini ve tam lisans metni var (REUSE uygulanıyorsa)
- [ ] Dosya başlığı şablonu CLAUDE.md'ye yazıldı (her yeni dosyada uygulanacak)
- [ ] `reuse lint` CI'a eklenecekler listesine girdi (Faz 2)