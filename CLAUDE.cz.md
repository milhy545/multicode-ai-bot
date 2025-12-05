# CLAUDE.cz.md

<div align="center">

[**English**](CLAUDE.md) | [**Čeština**](CLAUDE.cz.md)

</div>

Tento soubor poskytuje návod pro Claude Code (claude.ai/code) při práci s kódem v tomto repozitáři.

## Přehled Projektu

**MultiCode AI Telegram Bot** - Produkční multi-AI Telegram bot poskytující vzdálený přístup k více AI asistenům pro kódování (Claude, OpenAI, DeepSeek, Groq, atd.) přes Telegram messaging. Uživatelé mohou interagovat s různými AI providery pro analýzu kódu, operace se soubory, git příkazy a vývojové úkoly skrze terminálové rozhraní na jakémkoliv zařízení s Telegramem.

**Forknut z**: [claude-code-telegram](https://github.com/RichardAtCT/claude-code-telegram) od Richarda Atkinsona (MIT License)

**Tech Stack**: Python 3.10+, Poetry, python-telegram-bot, Anthropic SDK, OpenAI SDK, SQLite, structlog

## Vývojové Příkazy

### Setup & Instalace
```bash
make dev              # Instalovat všechny závislosti (dev + prod)
make install          # Instalovat pouze produkční závislosti
```

### Testování & Kvalita
```bash
make test             # Spustit pytest s coverage (cíl: >85%)
make lint             # Spustit black, isort, flake8, mypy kontroly
make format           # Auto-formátovat s black + isort
```

### Spuštění Bota
```bash
make run              # Spustit bota v produkčním módu
make run-debug        # Spustit s DEBUG loggingem (development)
```

### Utility
```bash
make clean            # Odstranit __pycache__, .pyc, test artifacts
make help             # Zobrazit všechny dostupné příkazy
```

### Spouštění Konkrétních Testů
```bash
poetry run pytest tests/unit/test_config.py           # Test konfiguračního systému
poetry run pytest tests/unit/test_claude/ -v          # Test Claude integrace
poetry run pytest -k "test_authentication" --verbose  # Test konkrétních vzorů
poetry run pytest --cov-report=html                   # Generovat HTML coverage report
```

## Architektura & Organizace Kódu

### High-Level Architektura

Kódová základna následuje **vrstvenou architekturu** s jasným oddělením zodpovědností:

1. **Bot Layer** (`src/bot/`): Telegram rozhraní, handlers, middleware
2. **Claude Integration** (`src/claude/`): SDK/CLI integrace, správa relací, monitorování nástrojů
3. **Security Layer** (`src/security/`): Autentizace, rate limiting, validace vstupů, audit logging
4. **Storage Layer** (`src/storage/`): Repository pattern, SQLite perzistence, session storage
5. **Configuration** (`src/config/`): Pydantic Settings v2, správa prostředí, feature flags

### Klíčové Integrační Body

**Claude Integration Facade** (`src/claude/facade.py`):
- High-level API: `ClaudeIntegration.run_command()` - jediný vstupní bod pro bot handlers
- Spravuje SDK vs CLI mód (konfigurovatelné přes `USE_SDK` nastavení)
- Automatický fallback z SDK na CLI při perzistentních selháních
- Integruje správu relací, monitorování nástrojů a streaming

**Bot Core** (`src/bot/core.py`):
- `ClaudeCodeBot` orchestruje všechny komponenty
- Dependency injection pattern: všechny služby předány přes `dependencies` dict
- Feature registry systém pro modulární správu funkcí
- Registrace handlerů probíhá v `_register_handlers()`

**Main Entry Point** (`src/main.py`):
- Inicializuje všechny služby (auth, storage, claude integration, security)
- Řeší graceful shutdown s signal handlery (SIGINT/SIGTERM)
- Konfiguruje structured logging (DEBUG vs INFO)
- CLI args: `--debug`, `--config-file`, `--version`
- Vytváří dependency injection dict pro inicializaci bota
- Fallback na allow-all auth v development módu když nejsou nakonfigurovány žádní provideři

## Konfigurace

### Požadované Proměnné Prostředí
```bash
TELEGRAM_BOT_TOKEN=1234567890:ABC...     # Od @BotFather
TELEGRAM_BOT_USERNAME=your_bot_name      # Bot username (bez @)
APPROVED_DIRECTORY=/path/to/projects     # Základní adresář (security sandbox)
```

### Claude Autentizace (Vyberte Jednu)
```bash
# Možnost 1: Použít existující CLI auth (doporučeno)
USE_SDK=true
# Není potřeba ANTHROPIC_API_KEY - použije CLI credentials

# Možnost 2: Přímý API klíč
USE_SDK=true
ANTHROPIC_API_KEY=sk-ant-api03-...

# Možnost 3: CLI subprocess mód (legacy)
USE_SDK=false
CLAUDE_CLI_PATH=/path/to/claude  # Volitelné, auto-detekce
```

### Důležitá Nastavení
- `ALLOWED_USERS`: Comma-separated Telegram user IDs (získejte od @userinfobot)
- `CLAUDE_ALLOWED_TOOLS`: Comma-separated whitelist nástrojů (viz `.env.example`)
- `CLAUDE_MAX_COST_PER_USER`: Limit výdajů na uživatele v USD
- `RATE_LIMIT_REQUESTS`/`RATE_LIMIT_WINDOW`: Konfigurace rate limitingu
- Feature flags: `ENABLE_GIT_INTEGRATION`, `ENABLE_FILE_UPLOADS`, `ENABLE_QUICK_ACTIONS`

Plná reference: `.env.example` má detailní popisy pro všech 50+ konfiguračních možností.

## Testovací Strategie

**Struktura Testů**: Testy zrcadlí strukturu `src/` v `tests/unit/`

**Klíčové Testovací Utility**:
- `create_test_config()`: Factory pro test Settings instance
- `@pytest.mark.asyncio`: Pro async test funkce
- `pytest-mock`: Mocking framework (použij `mocker` fixture)
- `pytest-cov`: Coverage reporting (cíl: >85%)

**Aktuální Coverage**: ~85% celkově (viz docs/development.md pro per-module rozdělení)

## Kódovací Standardy

### Type Safety
- **Všechny funkce musí mít type hints** (vynuceno mypy strict mode)
- Používej `Optional[T]` pro nullable hodnoty, `Union[A, B]` pro více typů
- Preferuj `Path` před `str` pro file paths
- Používej Pydantic modely pro strukturovaná data

### Error Handling
- Používej vlastní exception hierarchii v `src/exceptions.py`
- Základní exception: `ClaudeCodeTelegramError` (všechny vlastní exceptions dědí z tohoto)
- Specifické exceptions podle kategorie:
  - **Configuration**: `ConfigurationError`, `MissingConfigError`, `InvalidConfigError`
  - **Security**: `SecurityError`, `AuthenticationError`, `AuthorizationError`, `DirectoryTraversalError`
  - **Claude**: `ClaudeError`, `ClaudeTimeoutError`, `ClaudeProcessError`, `ClaudeParsingError`
  - **Storage**: `StorageError`, `DatabaseConnectionError`, `DataIntegrityError`
  - **Telegram**: `TelegramError`, `MessageTooLongError`, `RateLimitError`
- Vždy řeť exceptions: `raise NewError("message") from original_error`
- Loguj chyby se structured loggingem před raisem

### Logging
- Používej `structlog.get_logger()` ve všech modulech
- Zahrnuj kontext: `logger.info("message", user_id=123, operation="example")`
- Úrovně: DEBUG (verbose), INFO (normal), WARNING (issues), ERROR (failures)
- Produkce používá JSON logging; development používá console rendering

### Code Style
- **Black** formatting (88-char řádky) - vynuceno
- **isort** pro imports (Black-compatible profile) - vynuceno
- **flake8** linting s E203, W503 ignorováno - vynuceno
- **mypy** strict mode - vynuceno

Vždy spusť `make format` před commitem a `make lint` pro ověření.

## Bezpečnostní Úvahy

**Tento bot poskytuje přístup k file systému přes Claude Code. Bezpečnost je kritická.**

### Bezpečnostní Vrstvy
1. **Autentizace**: Whitelist-based (povinné) + volitelná token auth
2. **Directory Isolation**: Všechny cesty validované proti `APPROVED_DIRECTORY`
3. **Rate Limiting**: Token bucket algoritmus (requests + cost-based)
4. **Input Validation**: Ochrana proti injection, path traversal, zip bombs
5. **Tool Monitoring**: Validuje použití Claude nástrojů proti whitelistu
6. **Audit Logging**: Sleduje všechny bezpečnostní události s risk levels

## Důležité Poznámky

- **Nikdy necommituj secrets**: Používej `.env` pro citlivé hodnoty (už v `.gitignore`)
- **Bezpečnost migrací**: Databázové migrace auto-aplikovány při startu (viz `src/storage/database.py`)
- **Session persistence**: Relace uloženy v SQLite, přežijí restarty (nové v pokročilých funkcích)
- **SDK vs CLI mód**: SDK mód (default) je rychlejší a spolehlivější; CLI mód je legacy fallback
- **Cost tracking**: Veškeré API použití sledováno per uživatel, vynucuje `CLAUDE_MAX_COST_PER_USER` limit
- **Graceful shutdown**: SIGINT/SIGTERM správně zpracovány, relace uloženy při ukončení
- **Storage architektura**:
  - Session data: SQLite (perzistentní)
  - Audit logs: In-memory storage (TODO: migrovat do databáze pro produkci)
  - Auth tokens: In-memory storage (TODO: migrovat do databáze pro produkci)
- **Development mode fallback**: Když nejsou nakonfigurovani žádní auth provideři, dev mód povolí všem uživatelům (logováno varování)

## Stav Projektu

**Aktuální Fáze**: Feature-complete, production-ready (Multi-AI enhanced fork)
**Test Coverage**: ~85% (149/149 testů úspěšných k poslednímu commitu)
**Nedávné Přídavky**: Multi-AI podpora (8+ providerů), AI abstrakční vrstva, PyPI publishing, archive extraction, git integrace, quick actions, session export, image handling

**Fork Informace**:
- Původní projekt: claude-code-telegram od Richarda Atkinsona
- Fork vylepšení: Multi-AI podpora, provider abstrakce, rozšířená analytika
- Spravováno: milhy545

Viz `CHANGELOG.md` pro detailní historii verzí.
