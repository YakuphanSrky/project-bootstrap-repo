<!-- SPDX-License-Identifier: MIT -->

# ADR Rehberi

ADR (Architecture Decision Record), bir kararın **ne olduğunu değil, neden alındığını**
kaydeder. Değeri, karar değiştiğinde ortaya çıkar: eski gerekçeyi okuyup "bu gerekçe hâlâ
geçerli mi?" diye sorabilmek.

## İçindekiler

1. [Çekirdek / feature ayrımı](#çekirdek--feature-ayrımı)
2. [Durum yaşam döngüsü](#durum-yaşam-döngüsü)
3. [Supersede: yerinde revizyon mu, zincir mi?](#supersede-yerinde-revizyon-mu-zincir-mi)
4. [Kaldırılacaklar listesi](#kaldırılacaklar-listesi)
5. [Greplenebilir geri-referans](#greplenebilir-geri-referans)
6. [Drift audit](#drift-audit)
7. [Yazım ilkeleri](#yazım-ilkeleri)

---

## Çekirdek / feature ayrımı

Numara bloğunu baştan böl. Bu, hiçbir araç gerektirmeyen ama etkisi büyük bir sinyaldir:
numaraya bakan kişi kararın ağırlığını anlar.

| Blok | Kapsam | Değişme sıklığı |
|---|---|---|
| `ADR-0001` – `ADR-0009` | Çekirdek: stack, veritabanı, auth/authz, dizin şeması, kalite standardı | Nadiren — değişirse tüm proje etkilenir |
| `ADR-0010` – … | Feature kararları | Sık — etkisi sınırlı olmalı |

Bir çekirdek ADR değiştiğinde bu sıradan bir supersede değildir; drift audit'i tetikler
(aşağıya bak).

## Durum yaşam döngüsü

```
Proposed → Accepted → (Superseded | Deprecated | Suspended)
```

| Durum | Anlamı |
|---|---|
| **Proposed** | Tartışmaya açık. Bu ADR'ye dayanarak implementasyona **başlanmaz**. |
| **Accepted** | Yürürlükte. Kod buna uymak zorunda. |
| **Superseded by ADR-XXXX** | Başka bir kararla değiştirildi. Metni silinmez, tarihçe olarak kalır. |
| **Deprecated** | Geçersiz ama yerine bir şey konmadı. |
| **Suspended** | Kararları yanlış değil, ama uygulanacağı yüzey şu an yok. Koşullar dönerse aynen yürürlüğe girebilir. |

**Suspended, Superseded'den farklıdır ve bu fark önemlidir.** Bir kararı "yanlıştı" diye
işaretlemekle "doğruydu ama artık uygulanacak yer yok" demek aynı şey değildir; ikincisinde
karar geri gelebilir ve gerekçesi hâlâ okunabilir olmalıdır.

## Supersede: yerinde revizyon mu, zincir mi?

Bir kararı değiştirirken iki yol vardır. Yanlış olanı seçmek tarihçeyi ya kaybettirir ya
gereksiz şişirir.

**Yerinde revizyon** — hedef ADR henüz `Proposed` ise. Belgeyi doğrudan düzenle, zincir kurma.
Henüz yürürlüğe girmemiş bir karar için tarihçe tutmak gürültüdür.

**Supersede zinciri** — hedef ADR `Accepted` ise. Yeni bir ADR yaz; eskisini silme, başına
`Superseded by ADR-XXXX` notu düş.

**Kısmi supersede mümkündür ve tercih edilir.** Bir ADR'nin yalnız belirli maddeleri
geçersizleşiyorsa bunu açıkça yaz:

> `ADR-0013`, `ADR-0008` **D2**'nin yerine geçer; o belgenin D7 ve D8'i yürürlükte kalır.

Bu netlik olmadan, kısmen geçersiz bir belgenin hangi maddesine güvenileceği belirsizleşir —
ve pratikte insanlar ya tamamını geçersiz sayar ya tamamına güvenir; ikisi de yanlıştır.

## Kaldırılacaklar listesi

**Bu bölüm zorunludur ve bu skill'in çözdüğü asıl problemi çözer.** Bir ADR başka birini
geçersiz kıldığında, dokümanda karar değişir ama kod eski hâliyle kalır. Zamanla hangi kodun
hangi karara dayandığı belirsizleşir.

Bir kararı geçersiz kılan her ADR'ye şu bölümü ekle:

```markdown
## Kaldırılacaklar

Bu karar sonucu koddan silinmesi gerekenler:

- [ ] `scale_source` alanı — `/src/data-models/component.ts`
- [ ] `pHYs` okuma fonksiyonu — `/src/business-logic/scale-resolver.ts`
- [ ] "ölçeksiz kayıt" dalı — `/src/widgets/upload-flow.tsx`
- [ ] İlgili testler — `/tests/scale-resolver.test.ts`
- [ ] Veritabanı sütunu (migration ile) — `components.scale_source`
```

Bu listeyi bir issue'ya dönüştür ve temizlik tamamlanmadan ilgili MR'ı merge etme.
Karar ile kod arasındaki bağı zorlayıcı kılan tek mekanizma budur.

**Bir eşiği veya kuralı kaldırmadan önce sor:** sonucu *yükseltilebilir* mi? Uyarı üreten
bir eşik gereksiz görünüyorsa, doğru akıbeti silinmek değil sertleştirilmek olabilir.
Silinen bir eşiği geri getirmek, hiç kaldırmamaktan pahalıdır.

## Greplenebilir geri-referans

Kararın koddaki karşılığını aranabilir yap. Prose içinde anlatılan bir bağ, insan okuyup
hatırladığı sürece vardır; kod içindeki etiket her zaman bulunabilir.

```ts
// ADR-0011 D4 — çözünürlük tabanı
const MIN_DIAGONAL_PX = 360;
```

Bir ADR geçersizleştiğinde etkilenen her yer tek komutla bulunur:

```bash
grep -rn "ADR-0011 D4" src/ tests/
```

Etiket formatını CLAUDE.md'ye yaz ki tutarlı kalsın.

## Drift audit

Periyodik olarak (her milestone'da veya bir çekirdek ADR değiştiğinde) kararlarla kodun
hâlâ örtüşüp örtüşmediğini denetle:

1. `/docs/adr/` altındaki **Accepted** durumundaki kararları listele.
2. Her biri için koddaki karşılığını (geri-referans etiketleriyle) bul.
3. İki yönlü kontrol et:
   - **Uygulanmamış karar:** ADR yürürlükte ama kodda karşılığı yok.
   - **Dayanaksız kod:** Kod bir davranışı sürdürüyor ama dayandığı ADR superseded.
4. Bulguları issue'ya dönüştür.

Ölü kod tespit araçları (bkz. `kalite-araclari.md`) bu denetimin bir kısmını otomatikleştirir
ama tamamını değil: bir fonksiyon hâlâ çağrılıyor olabilir, sadece dayandığı karar geçersizdir.
Bunu yalnız ADR-kod eşlemesi yakalar.

## Yazım ilkeleri

**Gerekçeyi yaz, kararı değil.** "PostgreSQL kullanacağız" bir cümledir; "PostgreSQL
kullanacağız çünkü X, alternatif Y şu nedenle elendi" bir ADR'dir. Karar değiştiğinde
okunacak olan ikincisidir.

**Tek değerli alan iz taşımaz.** Yalnız tek bir değer üretebilen bir alan, aslında bilgi
taşımıyordur — kaldırılmalıdır. Bu ilke hem şema tasarımında hem ADR'lerin kendisinde
geçerlidir: her zaman aynı sonucu veren bir "karar", karar değildir.

**Ölçülemeyen eşik test edilemez.** Bir kural sayıya bağlanmıyorsa (ör. "görüntü yeterince
büyük olmalı"), test edilemez ve zamanla yorum farkına açılır. Eşikleri ADR'de sayıyla yaz,
kodda o sayıya geri-referans ver.

**Kapsam dışını açıkça yaz.** Bir ADR'nin "negatif sınırları" (bu karar neyi kapsamıyor),
kapsadıklarından daha çok tartışma önler.