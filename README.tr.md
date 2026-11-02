[![GitHub release](https://img.shields.io/github/v/release/sametbrr/look-again?display_name=tag&sort=semver)](https://github.com/sametbrr/look-again/releases/latest)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Agent Skills](https://img.shields.io/badge/agentskills.io-compatible-blue)](https://agentskills.io)

[Hızlı Başlangıç](#hızlı-başlangıç) • [Kurulum](#kurulum) • [Kullanım](#kullanım) • [Yapılandırma](#yapılandırma) • [Sınırlamalar](#sınırlamalar)

# Look Again

Konuşma checkpoint'lerini markdown dosyaları olarak kaydeden ve daha sonra tam bağlamla devam ettiren Claude Code ve Codex için bir Agent Skill ve plugin — kararlar, reddedilen alternatifler ve nihai plan birebir korunur.

> 🇬🇧 For English see [README.md](README.md)

---

## Hızlı Başlangıç

```bash
claude plugin marketplace add sametbrr/skill-hub && claude plugin install look-again@skill-hub   # Claude Code
codex plugin marketplace add sametbrr/skill-hub && codex plugin add look-again@skill-hub         # Codex
```

```
> "look again"          # save the current state as a checkpoint
> "look again list"     # list saved checkpoints, pick one to continue
```

Hepsi bu kadar — kayıtlar `~/.agents/look-again/` dizinine yazılır ve oturumlar arasında kalıcıdır. Claude Code ve Codex aynı klasörü kullanır; birinde kaydedip diğerinde devam edebilirsiniz.

---

## Özellikler

- **Yapılandırılmış anlık görüntüler** — hedef, gerekçeleriyle kesinleşmiş kararlar, reddedilen alternatifler, kısıtlar ve sıralı sonraki adımlar
- **Birebir sadakat** — nihai plan ve son istek/yanıt yazıldığı haliyle gömülür, asla özetlenmez
- **Kesintisiz devam** — bir kaydı açmak çalışma bağlamını geri yükler; kesinleşmiş kararlar yeniden sorulmaz
- **Frontmatter indeksi** — RECALL yalnızca frontmatter okuyarak listeler; büyük arşivler hızlı kalır
- **Ajanlar arası** — Claude Code ve Codex için tek ortak checkpoint klasörü; Claude.ai'de indir/yükle akışı

---

## Gereksinimler

- Claude Code, OpenAI Codex veya herhangi bir [agentskills.io](https://agentskills.io) uyumlu agent
- Claude.ai'de: `.md` dosyalarını indirip yeniden yükleyebilme (kalıcı disk yok)

---

## Kurulum

**Claude Code** (plugin)

```bash
claude plugin marketplace add sametbrr/skill-hub
claude plugin install look-again@skill-hub
```

**Codex** (plugin)

```bash
codex plugin marketplace add sametbrr/skill-hub
codex plugin add look-again@skill-hub
```

**Elle** (herhangi bir Agent Skills istemcisi): `skills/look-again/` klasörünü `~/.claude/skills/` ya da `~/.agents/skills/` altına kopyalayın.

Yeni bir oturum başlatın. Checkpoint kaydetme veya devam ettirmeden bahsettiğinizde skill otomatik yüklenir.

---

## Kullanım

Skill, modu doğal dilden algılar — Türkçe veya İngilizce:

```
> "look again"                     # SAVE: mevcut durumu checkpoint olarak kaydeder
> "tekrar bak"                     # SAVE, Türkçe
> "look again list"                # RECALL: kayıtları listeler, devam için birini seç
> "tekrar bakacaklarım nelerdi"    # RECALL, Türkçe
```

| Mod | Tetikleyici örnekler | Ne olur |
|---|---|---|
| **SAVE** | "look again", "tekrar bak", "save this for later", "bunu kaydet" | Checkpoint dosyası yazar: bağlam görüntüsü + nihai plan + son mesajlaşma birebir |
| **RECALL** | "look again list", "tekrar bakacaklarım nelerdi", "continue where we left off" | Kayıtları yeniden-eskiye listeler; birini seçince devam moduna geçilir |

Kayıtlar `YYYY-MM-DD-HHmm-<topic-slug>.md` olarak adlandırılır ve [skills/look-again/assets/entry-template.md](skills/look-again/assets/entry-template.md) şablonunu izler. Devam modunda asistan kayıtlı bağlamı benimser, reddedilen alternatifleri asla yeniden önermez ve kayıtlı sonraki adımlardan devam eder.

---

## Yapılandırma

### Checkpoint klasörü

| Sıra | Konum |
|---|---|
| 1 | `$LOOK_AGAIN_DIR` (tanımlıysa) |
| 2 | `~/.agents/look-again/` (varsayılan, Claude Code ve Codex ortak kullanır) |
| eski | `~/.claude/look-again/` (v1.1 ve öncesi): RECALL okumaya devam eder ve taşımayı önerir |

Codex'in `workspace-write` sandbox'ında çalışma alanı dışına yazma engellenebilir. Bu durumda skill onay ister ya da o seferlik `./.look-again/` altına kaydeder.

---

## Sınırlamalar

Claude.ai'de sandbox dosya sistemi konuşmalar arasında sıfırlanır; checkpoint'ler sunucu tarafında kalıcı olamaz.

**Geçici çözüm:** SAVE, kaydı indirilebilir dosya olarak sunar — yerelde saklayın (örn. `~/.agents/look-again/`) ve RECALL için gelecekteki bir sohbete yeniden yükleyin.

---

## Lisans

MIT — bkz. [LICENSE](LICENSE).
