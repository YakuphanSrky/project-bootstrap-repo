<!-- SPDX-License-Identifier: MIT -->

# CLAUDE.md — project-bootstrap

> Bu dosya Claude Code için proje bağlamını ve çalışma kurallarını tanımlar.
> **Yalnız güncel durumu** anlatır; kararların gerekçesi ve tarihçesi
> [`.notes/baglam.md`](.notes/baglam.md) ve (yazıldıkça) `docs/adr/` altındadır.
> Kullanıcı ile **Türkçe** konuş.

---

## 1. Proje Tanımı

Bu repo, **`project-bootstrap`** adlı bir [Claude Agent Skill](https://code.claude.com/docs/en/skills)
barındırır.

**Skill'in çözdüğü problem:** AI ile üretim hızlandıkça elle inceleme darboğaza dönüşür;
"gözle bakınca doğru görünüyor" ile "gerçekten doğru çalışıyor" arasındaki fark görünmez
hale gelir. Skill, kalitenin varsayım değil **ölçüm** olduğu, kararların kayıtlı ve kodun
kararlara bağlı olduğu bir iskeleti — her projede tekrarlanabilir biçimde — kurar.

Bu iskelet üç ayağa dayanır:

1. **Doğrulanabilir kalite** — statik analiz, testler ve CI kapıları; "şu standarda uy"
   talimatının gerçekten uygulandığını ölçen katman.
2. **Kayıtlı kararlar** — ADR disiplini; her kararın gerekçesi, kapsamı ve sınırı yazılı.
3. **Karar-kod bağı** — kaldırılacaklar listesi, greplenebilir geri-referans, testlerin
   temizlik kanıtı olması. Bu üçü, kararla kodun ayrışmasını (*drift*) önler.

> **Not:** Drift önleme, üç ayaktan **biridir** — en somut örneği olduğu için sık anılır
> ama skill'in tek derdi değildir. Ayrıntı: `.notes/baglam.md` §1.

**Bu repo kendi disiplinine tabidir** — skill neyi öğütlüyorsa, repo da onu uygular.

**Kapsam dışı:**
- Skill belirli bir dile/stack'e bağlı kod üretmez — metodoloji taşır, implementasyon değil.
- Ücretli araç önermez; önerilen doğrulama katmanının tamamı ücretsizdir.
- ISO/IEC 5055 **sertifikasyonu** iddia etmez; dört sütunu pratikte karşılamayı hedefler.

---

## 2. Ortam ve Artefakt

- **Artefakt:** `project-bootstrap/` klasörü (SKILL.md + references/ + assets/)
- **Dağıtım:** (a) Claude Code için klasör kopyalama, (b) Claude.ai için `.skill` paketi
- **Paketleme:** `.skill` **depoya girmez**; tag push edilince
  `.github/workflows/release.yml` üretir. Gerekçe: `.notes/baglam.md` K8.
- **Platform:** GitHub
- **Tek geçerli bilgi kaynağı:** bu deponun `main` branch'i

---

## 3. Lisans ve Telif

- **SPDX:** `MIT`
- Her kaynak dosya lisans başlığı taşır:

```
<!-- SPDX-License-Identifier: MIT -->
```
```yaml
# SPDX-License-Identifier: MIT
```

**Dikkat — iki tuzak:**

1. `SKILL.md`'de başlık **frontmatter'dan SONRA** gelir. `---` ilk satırda olmazsa skill
   hiç yüklenmez. (Bu hata bir kez yapıldı; `release.yml`'deki doğrulama adımı tekrarını
   engellemek için var.)
2. `assets/*.template` dosyalarındaki `{{LISANS_SPDX}}` yer tutucusu **kasıtlıdır** — o
   başlık şablonun kendi lisansı değil, şablondan üretilecek dosyanın lisansıdır. Değiştirme.

---

## 4. İş Akışı

```
issue → branch ({issue-no}-{kısa-ingilizce-kebab}) → commit(ler) → Pull Request
```

- **`main`'e doğrudan commit yok.**
- PR'ları **merge etme** — insan onayını bekle.
- PR açıklaması: `Closes #N` + değişikliğin gerekçesi.
- Commit: Conventional Commits, tip token'ı İngilizce, gövde Türkçe ve **neden**'i anlatır.

---

## 5. Kararlar ve Kalite

Bu deponun ADR disiplini [`ADR-0001`](docs/adr/ADR-0001-adr-discipline.md) ile bağlanmıştır;
aşağıdakiler onun özetidir, gerekçesi orada.

- Mimari kararlar `docs/adr/` altında ADR olarak kayda geçer.
- **Kapanmamış (Accepted olmamış) bir ADR üzerine implementasyona başlanmaz.** (D1)
- Format kaynağı `project-bootstrap/assets/ADR.md.template`; ayrışırsa **şablon kaynaktır**. (D2)
- Dosya adı: `docs/adr/ADR-NNNN-{kısa-ingilizce-kebab}.md` — dosya adı makine sınırıdır,
  İngilizce; başlık ve gövde Türkçe. (D3)
- Numara blokları: `ADR-0001`–`ADR-0009` çekirdek, `ADR-0010`+ feature. (D4)
- Durum döngüsü: `Proposed → Accepted → (Superseded | Deprecated | Suspended)`.
  `Suspended` ≠ `Superseded`. (D5)
- Hedef `Proposed` ise yerinde revizyon, `Accepted` ise supersede zinciri; kısmi supersede
  madde numarasıyla yazılır. (D6)
- Bir kararı geçersiz kılan ADR, **Kaldırılacaklar listesi** içerir; liste issue'ya dönüşür,
  temizlik bitmeden merge edilmez. (D7)
- **Geri-referans etiketi:** kodda `// ADR-NNNN DN`, markdown'da `<!-- ADR-NNNN DN -->`.
  Etiket madde numarasını içerir — kısmi supersede madde düzeyinde işlediği için ADR numarası
  tek başına yetmez. (D8)
- Çekirdek bir ADR değişirse **drift audit** tetiklenir. (D9)
- Skill'in davranışını değiştiren her PR gerekçesini açıklar.

---

## 6. Dizin Yapısı (HEDEF)

```
project-bootstrap-repo/
├── .github/workflows/release.yml
├── docs/
│   └── adr/                        # kararlar — ADR-0001'den itibaren
├── project-bootstrap/              # SKILL — asıl artefakt
│   ├── SKILL.md
│   ├── references/
│   │   ├── vcs-platformlari.md
│   │   ├── lisans.md
│   │   ├── adr-rehberi.md
│   │   └── kalite-araclari.md
│   └── assets/
│       ├── CLAUDE.md.template
│       ├── ADR.md.template
│       └── dizin-semasi.md
├── .gitignore
├── CLAUDE.md
├── LICENSE
└── README.md
```

`.notes/` — kişisel çalışma alanı, `.gitignore`'da. Depoya girmez, **yedeklenmez.**

`.notes/baglam.md` — tasarım gerekçelerinin kalıcı kaydı. Bilinçli olarak `.notes/` altında
tutuluyor; **depoya girmediği için başka bir makinede veya klonda bulunmaz.** Bu belgeye
yapılan atıflar (bkz. §9) yalnız bu makinede çözülür. Ayrıntı: `.notes/baglam.md` K10.

---

## 7. Yapı Durumu

§6'daki hedef yapı **kurulu**. Geçmişte dosyalar depoya düz açılmıştı; düzeltildi:

| Eski durum | Şimdi |
|---|---|
| `files/SKILL.md` | `project-bootstrap/SKILL.md` |
| `files/release.yml` | `.github/workflows/release.yml` |
| `Claude.md` | `CLAUDE.md` — büyük harfle (git adı olduğu gibi kaydeder; küçük harfle Linux'ta bulunamazdı) |
| `files/project-bootstrap.skill` | Silindi — `.gitignore`'da, release üretir |
| `references/` yok | `project-bootstrap/references/` — 4 dosya |
| `assets/` yok | `project-bootstrap/assets/` — 3 dosya |
| `README.md` tek satırlık placeholder | Gerçek README |

Eksik 7 dosya `project-bootstrap.skill` paketinden çıkarıldı; paketteki `SKILL.md` ile
`files/SKILL.md` birebir aynıydı (sha1 doğrulandı).

**Henüz yok:** `docs/adr/` — §8/2'nin işi.

Yapıyı doğrula:
```bash
head -1 project-bootstrap/SKILL.md     # '---' olmalı
find project-bootstrap -type f | wc -l # 8
ls CLAUDE.md                           # büyük harfle var mı
```

---

## 8. Sonraki Adımlar

Sırayla, her biri ayrı issue + branch + PR:

1. ~~**Yapıyı düzelt** (§7)~~ — tamam.
2. **Çekirdek ADR'ler.** Blok `ADR-0001`–`ADR-0009`; iki karar yeni, beşi retroaktif
   (gerekçeleri `.notes/baglam.md` §3'te hazır):

   | No | Karar | Tür | Dayanak |
   |---|---|---|---|
   | ~~0001~~ | ~~ADR disiplini~~ | retro | `adr-rehberi.md`, baglam K3+K4 |
   | 0002 | Kapsam ve problem sahipliği — AS1 burada kapanır | **yeni** | baglam §1, §5 |
   | 0003 | Artefakt biçimi, katman yapısı ve dağıtım | retro | baglam K1 + K8 ← 0002 |
   | 0004 | Etkileşim modeli + sınır: *gerçek fayda yoksa dayatma* | retro | baglam K5, K6 |
   | 0005 | Dil politikası | retro | baglam §2 |
   | 0006 | Lisans MIT + telif sahibi açık kalemi | retro | baglam K7 |
   | 0007 | Doğrulama standardı — skill nasıl test edilir | **yeni** | ← 0002, 0003, 0004 |
   | 0008–0009 | rezerve | — | — |

   Dalgalar: **0002** → **0003 + 0007** → **0004 + 0005 + 0006** (üçü düşük tartışmalı,
   §9'daki "gerçek bedel yoksa sorma" uyarınca tek PR).

   Şablon: `project-bootstrap/assets/ADR.md.template`
   Disiplin: `docs/adr/ADR-0001-adr-discipline.md`

3. **AS1 `ADR-0002`'nin konusudur** — skill üçe bölünecek mi? (`.notes/baglam.md` §5).
   **Test'ten önce karara bağlanmalı**, yoksa bölünecek bir yapıyı test etmiş oluruz.
4. **Skill'i test et** — 2-3 gerçekçi prompt, çıktı değerlendirmesi, düzeltme.
5. **İlk release** — `git tag v0.1.0 && git push --tags`; workflow'u doğrula.
6. ~~**README'deki `<kullanıcı>`** yer tutucusu~~ — tamam; depo adı da düzeltildi
   (`project-bootstrap` → `project-bootstrap-repo`).

---

## 9. Çalışma Tarzı

Skill aşamalı çalışmayı öğütlüyor; **onu geliştirirken de aynısını uygula**:

- **Tek soru → cevabı bekle → uygula → tek satır onayla → sonraki adım.** Aynı anda beş
  karar dayatma.
- **Gerçek bedel yoksa sorma** — faydası olan ve maliyeti olmayan adımı soru olarak sunmak
  gereksiz sürtünmedir; uygula ve bilgilendir.
- Skill metnini değiştirirken **şablon sabitliğini koru.**
- Bir şeyi neden yaptığını açıklarken `.notes/baglam.md`'ye bak — gerekçe orada yazılıdır,
  yeniden üretme.

---

## 10. Hızlı Kontrol Listesi

- [x] Yapı §6'ya uygun mu? (bkz. §7)
- [ ] İlgili ADR **Accepted** mi? Değilse implementasyona başlama. (ADR-0001 D1)
- [ ] Bir **issue** var mı? Yoksa önce issue.
- [ ] Branch `{issue-no}-{kebab}` ile mi? `main`'e doğrudan commit yok.
- [ ] Dosya **lisans başlığını** içeriyor mu? `SKILL.md`'de frontmatter'dan sonra mı?
- [ ] Bir ADR'ye dayanıyorsa **geri-referans etiketi** var mı? (ADR-0001 D8)
- [ ] `.skill` dosyası depoya girmiyor mu?
- [ ] Bir karar değiştiyse `.notes/baglam.md` güncellendi mi?
- [ ] PR açıldıysa: **merge etme**, insan onayını bekle.