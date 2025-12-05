# MultiCode AI Telegram Bot 🤖✨

<div align="center">

[**English**](README.md) | [**Čeština**](README.cz.md)

</div>

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.9+](https://img.shields.io/badge/python-3.9+-blue.svg)](https://www.python.org/downloads/)
[![Multi-AI](https://img.shields.io/badge/Multi--AI-8%20Providers-blue)](MULTI_AI_STATUS.md)
[![Test Coverage](https://img.shields.io/badge/coverage-85%25-brightgreen)](htmlcov/index.html)

> 🔧 **Fork a rozšíření projektu** [claude-code-telegram](https://github.com/RichardAtCT/claude-code-telegram) od Richarda Atkinsona (MIT License)

Výkonný Telegram bot poskytující vzdálený přístup k **8 různým AI asistentům pro kódování**, umožňující vývojářům pracovat na projektech odkudkoliv s perfektní AI pro každý úkol. Proměňte svůj telefon v vývojářský terminál s multi-AI asistencí, navigací v projektech a perzistentními relacemi.

> **🎉 KOMPLETNÍ:** 8 AI Providerů! Vyberte si z **Claude, Gemini, OpenAI, DeepSeek, Groq, Ollama, Blackbox a Windsurf**
>
> **6 ZDARMA možností** | **Ultra-levný DeepSeek** ($0.14/1M) | **Ultra-rychlý Groq** | **Lokální Ollama**
>
> [**Kompletní Multi-AI dokumentace →**](MULTI_AI_STATUS.md)

## ✨ Co to je?

Tento bot propojuje Telegram a **8 různých AI asistentů pro kódování**, což vám umožňuje:
- 💬 **Chatovat s 8 AI asistenty** o vašich kódových projektech přes Telegram
- 🔀 **Přepínat mezi AI** - vyberte perfektní nástroj pro každý úkol
- 📁 **Procházet adresáře** a spravovat soubory na dálku
- 🔄 **Uchovávat kontext** napříč konverzacemi s perzistentními relacemi
- 📱 **Kódovat na cestách** z jakéhokoliv zařízení s Telegramem
- 🛡️ **Zůstat v bezpečí** se zabudovanou autentizací a sandboxingem
- 💰 **Šetřit peníze** - 6 ZDARMA možností včetně Gemini, Groq a Ollama
- ⚡ **Ultra-rychlé odpovědi** s Groq LPU technologií
- 🏠 **100% soukromí** s lokálními Ollama modely
- 💸 **Ultra-levné** s DeepSeek za $0.14/1M tokenů

Perfektní pro code review na mobilu, rychlé opravy na cestách, nebo získání AI asistence mimo vývojářský stroj.

## 🤖 Vyberte si AI (8 Možností!)

### Premium Kvalita
- **Claude** ($3-15/1M) - Výjimečná kvalita, plné nástroje, komplexní uvažování 🏆
- **OpenAI GPT-4** ($10-60/1M) - Průmyslový standard, podpora vizí

### Budget/Zdarma Možnosti
- **DeepSeek** ($0.14-0.28/1M) - Specialista na kód, **10-20x levnější než GPT-4!** 💰
- **Gemini** (ZDARMA) - 1M token kontext, vize, nepotřebuje kreditku 🆓
- **Groq** (ZDARMA) - Ultra-rychlá LPU inference, Llama 3/Mixtral ⚡
- **Ollama** (ZDARMA) - Lokální modely, naprosté soukromí, offline 🏠
- **Blackbox** (ZDARMA) - Kódově-zaměřené web API 🆓
- **Windsurf** (ZDARMA) - Codeium cascade architektura 🆓

[**Plné porovnání providerů →**](MULTI_AI_STATUS.md)

## 🚀 Rychlý Start

**Vyberte způsob instalace:**

| Metoda | Nejlepší pro | Instalační příkaz |
|--------|--------------|-------------------|
| 🚀 **curl\|bash** | Nejrychlejší setup | `curl -fsSL https://raw.githubusercontent.com/milhy545/multicode-ai-bot/main/install.sh \| bash` |
| 🐳 **Docker** | Produkční servery | `docker-compose up -d` |
| 📦 **PyPI** | Python projekty | `pip install multicode-ai-bot` |
| 📱 **Flatpak** | Linux desktop | `flatpak install multicode-bot` |
| 💿 **AppImage** | Přenosný Linux | Stáhnout & spustit |

**[📖 Kompletní instalační průvodce →](INSTALLATION.md)** | **[🐳 Docker průvodce →](DOCKER.md)**

### Instalace jedním příkazem (Doporučeno):

```bash
curl -fsSL https://raw.githubusercontent.com/milhy545/multicode-ai-bot/main/install.sh | bash
```

Poté nakonfigurujte `.env` a spusťte:
```bash
cd ~/.multicode-bot
nano .env
./multicode-bot
```

### Demo
```
Vy: cd my-project
Bot: 📂 Změněno na: my-project/

Vy: ls
Bot: 📁 src/
     📁 tests/
     📄 README.md
     📄 package.json

Vy: Můžeš mi pomoct přidat error handling do src/api.py?
Bot: 🤖 Pomůžu ti přidat robustní error handling do API...
     [Claude analyzuje kód a navrhuje vylepšení]
```

## 🛠️ Instalace

### Požadavky

- **Python 3.9+** - [Stáhnout zde](https://www.python.org/downloads/)
- **Poetry** - Moderní Python dependency management
- **Claude Code CLI** - [Instalace odsud](https://claude.ai/code)
- **Telegram Bot Token** - Získejte od [@BotFather](https://t.me/botfather)

### 1. Získejte Bot Token

1. Napište [@BotFather](https://t.me/botfather) na Telegramu
2. Pošlete `/newbot` a postupujte podle pokynů
3. Uložte si váš bot token (vypadá jako `1234567890:ABC...`)
4. Zapamatujte si username bota (např. `my_claude_bot`)

### 2. Nastavení AI Providerů

Můžete použít jednoho nebo více AI providerů! Zde je návod pro každého:

**ZDARMA Provideři (Nepotřebují API klíč):**

```bash
# Blackbox - Žádné nastavení, funguje okamžitě!
DEFAULT_AI_PROVIDER=blackbox

# Ollama - Instalujte lokálně pro 100% soukromí
brew install ollama  # nebo stáhnout z ollama.ai
ollama pull codellama
DEFAULT_AI_PROVIDER=ollama
```

**ZDARMA Provideři (Vyžadují API klíč ale mají free tier):**

```bash
# Gemini - Získejte zdarma API klíč z https://aistudio.google.com/app/apikey
GEMINI_API_KEY=your_key_here
DEFAULT_AI_PROVIDER=gemini

# Groq - Ultra-rychlý, získejte zdarma klíč z https://console.groq.com/
GROQ_API_KEY=your_key_here
DEFAULT_AI_PROVIDER=groq
```

**Placení Provideři:**

```bash
# Claude - Nejlepší kvalita (Možnost 1: CLI auth nebo Možnost 2: API klíč)
ANTHROPIC_API_KEY=your_key_here
DEFAULT_AI_PROVIDER=claude

# OpenAI - Průmyslový standard
OPENAI_API_KEY=your_key_here
DEFAULT_AI_PROVIDER=openai

# DeepSeek - Ultra-levný ($0.14/1M!)
DEEPSEEK_API_KEY=your_key_here
DEFAULT_AI_PROVIDER=deepseek
```

**Doporučení:** Začněte s **Blackbox** (okamžité, žádné nastavení) nebo **Gemini** (ZDARMA, 1M kontext)!

### 3. Instalace Bota

```bash
# Klonujte repozitář
git clone https://github.com/milhy545/multicode-ai-bot.git
cd multicode-ai-bot

# Instalujte Poetry (pokud potřebujete)
curl -sSL https://install.python-poetry.org | python3 -

# Instalujte závislosti
make dev
```

### 4. Konfigurace Prostředí

```bash
# Zkopírujte příklad konfigurace
cp .env.example .env

# Upravte se svými nastaveními
nano .env
```

**Minimální požadovaná konfigurace:**
```bash
TELEGRAM_BOT_TOKEN=1234567890:ABC-DEF1234ghIkl-zyx57W2v1u123ew11
TELEGRAM_BOT_USERNAME=my_bot_name
APPROVED_DIRECTORY=/Users/yourname/projects
ALLOWED_USERS=123456789  # Vaše Telegram user ID
```

### 5. Spuštění Bota

```bash
# Spustit v debug módu
make run-debug

# Nebo pro produkci
make run
```

🎉 **Hotovo!** Napište svému botovi na Telegramu!

## 📱 Použití

### Základní Příkazy

#### Navigační Příkazy
```
/ls                    # Vypsat soubory v aktuálním adresáři
/cd myproject         # Změnit na projektový adresář
/pwd                  # Zobrazit aktuální adresář
/projects             # Zobrazit dostupné projekty
```

#### Správa Relací
```
/new                  # Začít novou Claude relaci
/continue [zpráva]    # Pokračovat v předchozí relaci
/end                  # Ukončit aktuální relaci
/status               # Zobrazit stav relace a využití
/export               # Exportovat relaci (Markdown, HTML, JSON)
```

#### Pokročilé Funkce
```
/git                  # Zobrazit git repository info
/actions              # Zobrazit kontextové rychlé akce
```

#### Nápověda
```
/start                # Uvítací zpráva a nastavení
/help                 # Zobrazit všechny dostupné příkazy
```

### Komunikace s Claude

Jen pošlete jakoukoli zprávu pro interakci s Claude o vašem kódu:

```
Vy: "Analyzuj tuto Python funkci na potenciální chyby"
Vy: "Pomoz mi optimalizovat tento databázový dotaz"
Vy: "Vytvoř React komponentu pro autentizaci uživatele"
Vy: "Vysvětli co dělá tento kód"
```

## 🛡️ Bezpečnost

Tento bot implementuje enterprise-grade bezpečnost:

- **🔐 Řízení Přístupu**: Whitelist-based autentizace uživatelů
- **📁 Izolace Adresářů**: Striktní sandboxing do schválených adresářů
- **⏱️ Rate Limiting**: Request a cost-based limity prevence zneužití
- **🛡️ Validace Vstupů**: Ochrana proti injection útokům
- **📊 Audit Logging**: Kompletní sledování všech uživatelských akcí
- **🔒 Bezpečné Výchozí**: Princip nejmenších oprávnění všude

Pro bezpečnostní problémy, viz [SECURITY.md](SECURITY.md).

## 🤝 Přispívání

Vítáme příspěvky! Zde je jak začít:

### Vývojové Příkazy

```bash
make help          # Zobrazit všechny dostupné příkazy
make test          # Spustit testy s coverage
make lint          # Spustit kontroly kvality kódu
make format        # Auto-formátovat kód
make run-debug     # Spustit bota v debug módu
```

### Pravidla Přispívání

1. 🍴 **Forkněte** repozitář
2. 🌿 **Vytvořte** feature branch: `git checkout -b feature/amazing-feature`
3. ✨ **Proveďte** své změny s testy
4. ✅ **Otestujte** své změny: `make test && make lint`
5. 📝 **Commitněte** své změny: `git commit -m 'Add amazing feature'`
6. 🚀 **Pushněte** do branch: `git push origin feature/amazing-feature`
7. 🎯 **Submitněte** Pull Request

### Kódovací Standardy

- **Python 3.9+** s type hints
- **Black** formatting (88 char délka řádku)
- **pytest** pro testování s >85% coverage
- **mypy** pro statickou kontrolu typů
- **Conventional commits** pro commit zprávy

## 📄 Licence

Tento projekt je licencován pod MIT License - viz [LICENSE](LICENSE) soubor pro detaily.

## 🌟 Star Historie

Pokud vám tento projekt připadá užitečný, prosím zvažte dání hvězdičky! ⭐

## 🙏 Kredity & Poděkování

### Původní Projekt
Tento projekt je fork [claude-code-telegram](https://github.com/RichardAtCT/claude-code-telegram) od **Richarda Atkinsona**.

- **Původní Autor**: Richard Atkinson ([GitHub](https://github.com/RichardAtCT))
- **Původní Licence**: MIT License (zachováno v tomto forku)
- **Původní Koncept**: Telegram bot pro vzdálený Claude Code přístup

### Správce Forku
- **Rozšířeno**: milhy545 ([GitHub](https://github.com/milhy545))
- **Fork Započat**: 2024
- **Hlavní Změny**: Multi-AI podpora (8 providerů), AI abstrakční vrstva, PyPI publishing, rozšířené funkce

### Speciální Poděkování
- [Claude](https://claude.ai) od Anthropic za úžasné AI schopnosti
- [OpenAI](https://openai.com) za GPT modely
- [DeepSeek](https://deepseek.com), [Groq](https://groq.com) a všem týmům AI providerů
- [python-telegram-bot](https://github.com/python-telegram-bot/python-telegram-bot) za výbornou Telegram integraci
- Všem přispěvatelům, kteří pomáhají zlepšovat oba projekty

### Přispívání Zpět
Ačkoliv se tento fork výrazně odlišil od původního projektu, uznáváme a respektujeme základ poskytnutý prací Richarda Atkinsona. Pokud hledáte jednodušší, Claude-zaměřené řešení, podívejte se na [původní projekt](https://github.com/RichardAtCT/claude-code-telegram).

---

**Vytvořeno s ❤️ pro vývojáře, kteří kódují na cestách**
