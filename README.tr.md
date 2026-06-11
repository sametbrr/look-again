[![GitHub release](https://img.shields.io/github/v/release/sametbrr/look-again?display_name=tag&sort=semver)](https://github.com/sametbrr/look-again/releases/latest)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Agent Skills](https://img.shields.io/badge/agentskills.io-compatible-blue)](https://agentskills.io)

# Look Again

Konuşma checkpoint'lerini markdown dosyaları olarak kaydeden ve daha sonra tam bağlamla devam ettiren bir Claude Code skill'i — kararlar, reddedilen alternatifler ve nihai plan birebir korunur.

> 🇬🇧 For English see [README.md](README.md)

---

## Hızlı Başlangıç

```bash
git clone https://github.com/sametbrr/look-again ~/.claude/skills/look-again
```

```
> "look again"          # save the current state as a checkpoint
> "look again list"     # list saved checkpoints, pick one to continue
```

Hepsi bu kadar — kayıtlar `~/.claude/look-again/` dizinine yazılır ve oturumlar arasında kalıcıdır.

---

## Özellikler

- **Yapılandırılmış anlık görüntüler** — hedef, gerekçeleriyle kesinleşmiş kararlar, reddedilen alternatifler, kısıtlar ve sıralı sonraki adımlar
- **Birebir sadakat** — nihai plan ve son istek/yanıt yazıldığı haliyle gömülür, asla özetlenmez
- **Kesintisiz devam** — bir kaydı açmak çalışma bağlamını geri yükler; kesinleşmiş kararlar yeniden sorulmaz
- **Frontmatter indeksi** — RECALL yalnızca frontmatter okuyarak listeler; büyük arşivler hızlı kalır
- **Çift ortam** — Claude Code'da kalıcı disk, Claude.ai'de indir/yükle akışı

---

## Gereksinimler

- Claude Code (veya herhangi bir [agentskills.io](https://agentskills.io) uyumlu agent)
- Claude.ai'de: `.md` dosyalarını indirip yeniden yükleyebilme (kalıcı disk yok)

---

## Kurulum

```bash
git clone https://github.com/sametbrr/look-again ~/.claude/skills/look-again
```

Yeni bir Claude Code oturumu başlatın. Checkpoint kaydetme veya devam ettirmeden bahsettiğinizde skill otomatik yüklenir.

---

## Kullanım

Skill, modu doğal dilden algılar — Türkçe veya İngilizce:

| Mod | Tetikleyici örnekler | Ne olur |
|---|---|---|
| **SAVE** | "look again", "tekrar bak", "save this for later", "bunu kaydet" | Checkpoint dosyası yazar: bağlam görüntüsü + nihai plan + son mesajlaşma birebir |
| **RECALL** | "look again list", "tekrar bakacaklarım nelerdi", "continue where we left off" | Kayıtları yeniden-eskiye listeler; birini seçince devam moduna geçilir |

Kayıtlar `YYYY-MM-DD-HHmm-<topic-slug>.md` olarak adlandırılır ve [assets/entry-template.md](assets/entry-template.md) şablonunu izler. Devam modunda asistan kayıtlı bağlamı benimser, reddedilen alternatifleri asla yeniden önermez ve kayıtlı sonraki adımlardan devam eder.

---

## Sınırlamalar

Claude.ai'de sandbox dosya sistemi konuşmalar arasında sıfırlanır; checkpoint'ler sunucu tarafında kalıcı olamaz.

**Geçici çözüm:** SAVE, kaydı indirilebilir dosya olarak sunar — yerelde saklayın (örn. `~/.claude/look-again/`) ve RECALL için gelecekteki bir sohbete yeniden yükleyin.

---

## Lisans

MIT — bkz. [LICENSE](LICENSE).
