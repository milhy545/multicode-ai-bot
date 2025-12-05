# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**MultiCode AI Telegram Bot** - A production-grade multi-AI Telegram bot that provides remote access to multiple AI coding assistants (Claude, OpenAI, DeepSeek, Groq, etc.) via Telegram messaging. Users can interact with various AI providers to perform code analysis, file operations, git commands, and development tasks through a terminal-like interface on any device with Telegram.

**Forked from**: [claude-code-telegram](https://github.com/RichardAtCT/claude-code-telegram) by Richard Atkinson (MIT License)

**Tech Stack**: Python 3.10+, Poetry, python-telegram-bot, Anthropic SDK, OpenAI SDK, SQLite, structlog

## Development Commands

### Setup & Installation
```bash
make dev              # Install all dependencies (dev + prod)
make install          # Install production dependencies only
```

### Testing & Quality
```bash
make test             # Run pytest with coverage (target: >85%)
make lint             # Run black, isort, flake8, mypy checks
make format           # Auto-format with black + isort
```

### Running the Bot
```bash
make run              # Run bot in production mode
make run-debug        # Run with DEBUG logging (development)
```

### Utilities
```bash
make clean            # Remove __pycache__, .pyc, test artifacts
make help             # Show all available commands
```

### Running Specific Tests
```bash
poetry run pytest tests/unit/test_config.py           # Test configuration system
poetry run pytest tests/unit/test_claude/ -v          # Test Claude integration
poetry run pytest -k "test_authentication" --verbose  # Test specific patterns
poetry run pytest --cov-report=html                   # Generate HTML coverage report
```

## Architecture & Code Organization

### High-Level Architecture

The codebase follows a **layered architecture** with clear separation of concerns:

1. **Bot Layer** (`src/bot/`): Telegram interface, handlers, middleware
2. **Claude Integration** (`src/claude/`): SDK/CLI integration, session management, tool monitoring
3. **Security Layer** (`src/security/`): Authentication, rate limiting, input validation, audit logging
4. **Storage Layer** (`src/storage/`): Repository pattern, SQLite persistence, session storage
5. **Configuration** (`src/config/`): Pydantic Settings v2, environment management, feature flags

### Key Integration Points

**Claude Integration Facade** (`src/claude/facade.py`):
- High-level API: `ClaudeIntegration.run_command()` - single entry point for bot handlers
- Manages SDK vs CLI mode (configurable via `USE_SDK` setting)
- Automatic fallback from SDK to CLI on persistent failures
- Integrates session management, tool monitoring, and streaming

**Bot Core** (`src/bot/core.py`):
- `ClaudeCodeBot` orchestrates all components
- Dependency injection pattern: all services passed via `dependencies` dict
- Feature registry system for modular feature management
- Handler registration happens in `_register_handlers()`

**Main Entry Point** (`src/main.py`):
- Initializes all services (auth, storage, claude integration, security)
- Handles graceful shutdown with signal handlers (SIGINT/SIGTERM)
- Configures structured logging (DEBUG vs INFO)
- CLI args: `--debug`, `--config-file`, `--version`
- Creates dependency injection dict for bot initialization
- Falls back to allow-all auth in development mode when no providers configured

### Module Responsibilities

**config/** - Configuration management with Pydantic Settings v2
- `settings.py`: Main Settings class with validation
- `loader.py`: Environment detection and config loading
- `environments.py`: Environment-specific overrides (dev/test/prod)
- `features.py`: Feature flag system for toggling functionality

**bot/handlers/** - Telegram command and message handlers
- `command.py`: Command handlers (/start, /help, /cd, /ls, /git, etc.)
- `message.py`: Text message handler (forwards to Claude)
- `callback.py`: Inline keyboard callback handlers

**bot/features/** - Advanced feature implementations
- `file_handler.py`: File uploads, archive extraction
- `git_integration.py`: Safe git operations (status, diff, log)
- `quick_actions.py`: Context-aware action buttons
- `session_export.py`: Export sessions (Markdown, HTML, JSON)
- `image_handler.py`: Image upload and analysis
- `conversation_mode.py`: Follow-up suggestions

**claude/** - Claude Code integration
- `facade.py`: High-level integration API
- `sdk_integration.py`: Anthropic Python SDK implementation
- `integration.py`: CLI subprocess management (legacy)
- `session.py`: Session state and context management
- `monitor.py`: Tool usage monitoring and validation
- `parser.py`: Response parsing and formatting

**security/** - Security framework
- `auth.py`: Multi-provider authentication (whitelist + token)
- `rate_limiter.py`: Token bucket rate limiting
- `validators.py`: Input validation, path traversal prevention
- `audit.py`: Security event logging

**storage/** - Data persistence
- `database.py`: SQLite connection, migrations
- `repositories.py`: Repository pattern for data access
- `session_storage.py`: Persistent session storage
- `facade.py`: Storage abstraction layer
- `models.py`: Pydantic models for type-safe data

## Configuration

### Required Environment Variables
```bash
TELEGRAM_BOT_TOKEN=1234567890:ABC...     # From @BotFather
TELEGRAM_BOT_USERNAME=your_bot_name      # Bot username (no @)
APPROVED_DIRECTORY=/path/to/projects     # Base directory (security sandbox)
```

### Claude Authentication (Choose One)
```bash
# Option 1: Use existing CLI auth (recommended)
USE_SDK=true
# No ANTHROPIC_API_KEY needed - uses CLI credentials

# Option 2: Direct API key
USE_SDK=true
ANTHROPIC_API_KEY=sk-ant-api03-...

# Option 3: CLI subprocess mode (legacy)
USE_SDK=false
CLAUDE_CLI_PATH=/path/to/claude  # Optional, auto-detected
```

### Important Settings
- `ALLOWED_USERS`: Comma-separated Telegram user IDs (get from @userinfobot)
- `CLAUDE_ALLOWED_TOOLS`: Comma-separated tool whitelist (see `.env.example`)
- `CLAUDE_MAX_COST_PER_USER`: Spending limit per user in USD
- `RATE_LIMIT_REQUESTS`/`RATE_LIMIT_WINDOW`: Rate limiting configuration
- Feature flags: `ENABLE_GIT_INTEGRATION`, `ENABLE_FILE_UPLOADS`, `ENABLE_QUICK_ACTIONS`

Full reference: `.env.example` has detailed descriptions for all 50+ configuration options.

## Testing Strategy

**Test Structure**: Tests mirror `src/` structure in `tests/unit/`

**Key Testing Utilities**:
- `create_test_config()`: Factory for test Settings instances
- `@pytest.mark.asyncio`: For async test functions
- `pytest-mock`: Mocking framework (use `mocker` fixture)
- `pytest-cov`: Coverage reporting (target: >85%)

**Testing Patterns**:
```python
# Configuration testing
def test_feature():
    config = create_test_config(debug=True, claude_max_turns=5)
    assert config.debug is True

# Async testing
@pytest.mark.asyncio
async def test_async_feature(mocker):
    mock_client = mocker.Mock()
    result = await some_async_function(mock_client)
    assert result is not None
```

**Current Coverage**: ~85% overall (see docs/development.md for per-module breakdown)

## Code Standards

### Type Safety
- **All functions must have type hints** (enforced by mypy strict mode)
- Use `Optional[T]` for nullable values, `Union[A, B]` for multiple types
- Prefer `Path` over `str` for file paths
- Use Pydantic models for structured data

### Error Handling
- Use custom exception hierarchy in `src/exceptions.py`
- Base exception: `ClaudeCodeTelegramError` (all custom exceptions inherit from this)
- Specific exceptions by category:
  - **Configuration**: `ConfigurationError`, `MissingConfigError`, `InvalidConfigError`
  - **Security**: `SecurityError`, `AuthenticationError`, `AuthorizationError`, `DirectoryTraversalError`
  - **Claude**: `ClaudeError`, `ClaudeTimeoutError`, `ClaudeProcessError`, `ClaudeParsingError`
  - **Storage**: `StorageError`, `DatabaseConnectionError`, `DataIntegrityError`
  - **Telegram**: `TelegramError`, `MessageTooLongError`, `RateLimitError`
- Always chain exceptions: `raise NewError("message") from original_error`
- Log errors with structured logging before raising

### Logging
- Use `structlog.get_logger()` in all modules
- Include context: `logger.info("message", user_id=123, operation="example")`
- Levels: DEBUG (verbose), INFO (normal), WARNING (issues), ERROR (failures)
- Production uses JSON logging; development uses console rendering

### Code Style
- **Black** formatting (88-char lines) - enforced
- **isort** for imports (Black-compatible profile) - enforced
- **flake8** linting with E203, W503 ignored - enforced
- **mypy** strict mode - enforced

Always run `make format` before committing and `make lint` to verify.

## Security Considerations

**This bot provides file system access via Claude Code. Security is critical.**

### Security Layers
1. **Authentication**: Whitelist-based (required) + optional token auth
2. **Directory Isolation**: All paths validated against `APPROVED_DIRECTORY`
3. **Rate Limiting**: Token bucket algorithm (requests + cost-based)
4. **Input Validation**: Protection against injection, path traversal, zip bombs
5. **Tool Monitoring**: Validates Claude tool usage against whitelist
6. **Audit Logging**: Tracks all security events with risk levels

### Path Validation
All user-provided paths go through `SecurityValidator.validate_path()`:
- Checks against `APPROVED_DIRECTORY` sandbox
- Prevents path traversal (`..`, symlinks)
- Validates absolute vs relative paths
- Returns `SecurityValidationResult` with risk assessment

### Claude Tool Whitelist
Configure `CLAUDE_ALLOWED_TOOLS` to restrict which Claude tools can be used:
- Safe for most users: `Read,Grep,Glob,LS` (read-only)
- Allow file changes: Add `Write,Edit,MultiEdit`
- Allow command execution: Add `Bash,Task` (higher risk)
- Full access: Include all tools (only for trusted users)

## Common Development Tasks

### Adding a New Command
1. Add handler in `src/bot/handlers/command.py`:
   ```python
   async def my_command_handler(update: Update, context: ContextTypes.DEFAULT_TYPE):
       await update.message.reply_text("Response")
   ```
2. Register in `src/bot/core.py` `_register_handlers()`:
   ```python
   self.app.add_handler(CommandHandler("mycommand", handlers.my_command_handler))
   ```
3. Add to bot menu in `_set_bot_commands()`:
   ```python
   BotCommand("mycommand", "Description of command")
   ```
4. Add tests in `tests/unit/test_bot/test_handlers/test_command.py`

### Adding a New Feature Flag
1. Add to `Settings` class in `src/config/settings.py`:
   ```python
   enable_my_feature: bool = Field(False, description="Enable my feature")
   ```
2. Add property to `FeatureFlags` in `src/config/features.py`:
   ```python
   @property
   def my_feature_enabled(self) -> bool:
       return self.settings.enable_my_feature
   ```
3. Check in code: `if self.config.features.my_feature_enabled:`
4. Document in `.env.example`
5. Add tests

### Debugging Issues
```bash
# Run with verbose logging
make run-debug

# Check specific test with output
poetry run pytest tests/path/to/test.py -vv -s

# Verify configuration loading
poetry run python -c "from src.config import load_config; print(load_config().model_dump())"

# Test authentication
# Set ALLOWED_USERS to your Telegram ID (get from @userinfobot)

# Check database
sqlite3 data/bot.db ".schema"  # View schema
sqlite3 data/bot.db "SELECT * FROM sessions;"  # View sessions
```

## Important Notes

- **Never commit secrets**: Use `.env` for sensitive values (already in `.gitignore`)
- **Migration safety**: Database migrations are auto-applied on startup (see `src/storage/database.py`)
- **Session persistence**: Sessions stored in SQLite, survive restarts (new in advanced features)
- **SDK vs CLI mode**: SDK mode (default) is faster and more reliable; CLI mode is legacy fallback
- **Cost tracking**: All API usage tracked per user, enforces `CLAUDE_MAX_COST_PER_USER` limit
- **Graceful shutdown**: SIGINT/SIGTERM handled properly, sessions saved on exit
- **Storage architecture**:
  - Session data: SQLite (persistent)
  - Audit logs: In-memory storage (TODO: migrate to database for production)
  - Auth tokens: In-memory storage (TODO: migrate to database for production)
- **Development mode fallback**: When no auth providers configured, dev mode allows all users (warning logged)

## Project Status

**Current Phase**: Feature-complete, production-ready (Multi-AI enhanced fork)
**Test Coverage**: ~85% (149/149 tests passing as of last commit)
**Recent Additions**: Multi-AI support (8+ providers), AI abstraction layer, PyPI publishing, archive extraction, git integration, quick actions, session export, image handling

**Fork Information**:
- Original project: claude-code-telegram by Richard Atkinson
- Fork enhancements: Multi-AI support, provider abstraction, enhanced analytics
- Maintained by: milhy545

See `CHANGELOG.md` for detailed version history.
