# Perp Dex Trading Bot

An Aster Perp DEX Trading (perpetual contracts trading) bot featuring built-in trend following (SMA30) and market making strategies. Uses WebSocket real-time market data with an Ink-driven command-line interface and automatic recovery from disconnections.

## Contact

If you need this kind of perps dex or perps trading bot, contact here: [Telegram](https://t.me/shiny0103) | [Twitter](https://x.com/0xTan1319)

## Features

- **Live WebSocket data** with REST fallbacks and automatic re-sync after reconnects
- **Trend Strategy**: SMA30 crossover entries, automated stop-loss/trailing-stop, and P&L tracking
- **Market Making Strategy**: Adaptive bid/ask chasing, risk stops, and order introspection
- **Modular Architecture**: Exchange gateway, engines, and UI components are modular for easy extension

## Requirements

- [Bun](https://bun.com) ≥ 1.2
- Node.js (optional, only if you prefer `npm` tooling)
- Valid Aster API credentials with futures trading access

## Configuration

Create an `.env` file (or export environment variables) with at least:
```bash
ASTER_API_KEY=your_key
ASTER_API_SECRET=your_secret
TRADE_SYMBOL=BTCUSDT        # optional, defaults to BTCUSDT
TRADE_AMOUNT=0.001          # position size used by both strategies
LOSS_LIMIT=0.03             # per-trade USD loss cap
TRAILING_PROFIT=0.2         # trailing activation profit in USDT
TRAILING_CALLBACK_RATE=0.2  # trailing callback in percent, e.g. 0.2 => 0.2%
PROFIT_LOCK_TRIGGER_USD=0.1 # profit threshold to start moving base stop (USDT)
PROFIT_LOCK_OFFSET_USD=0.05 # base stop offset from entry after trigger (USDT)
PRICE_TICK=0.1              # price tick size; set per symbol
QTY_STEP=0.001              # quantity step size; set per symbol
```

Additional maker-specific settings (`MAKER_*`) are in `src/config.ts` and can be overridden via environment variables:
```bash
# Maker-specific (units in USDT unless noted)
MAKER_LOSS_LIMIT=0.03             # override maker risk stop; defaults to LOSS_LIMIT
MAKER_PRICE_CHASE=0.3             # chase threshold
MAKER_BID_OFFSET=0                # bid offset from top bid (USDT)
MAKER_ASK_OFFSET=0                # ask offset from top ask (USDT)
MAKER_REFRESH_INTERVAL_MS=1500    # maker refresh cadence (ms)
MAKER_MAX_CLOSE_SLIPPAGE_PCT=0.05 # allowed deviation vs mark when closing
MAKER_PRICE_TICK=0.1              # maker tick size; defaults to PRICE_TICK
```

## Running the CLI

```bash
bun run index.ts   # or: bun run dev / bun run start
```

Select a strategy using arrow keys. Press `Esc` to return to the menu. The dashboard displays live order books, holdings, pending orders, and recent events. All state is synchronized with exchange data and automatically restored on restart.

## Testing

```bash
bun run test        # bun x vitest run
bun run test:watch  # stay in watch mode
```

Current tests cover order coordinator utilities and strategy helpers. Add unit tests alongside new modules as you extend the system.

## Project Structure

- `src/config.ts` – shared runtime configuration
- `src/core/` – trend & maker engines plus order coordination
- `src/exchanges/` – Aster REST/WS gateway and adapters
- `src/ui/` – Ink components and strategy dashboards
- `src/utils/` – math helpers and strategy utilities
- `tests/` – Vitest test suites for critical modules

## Troubleshooting

- **WebSocket reconnect loops**: Ensure outbound access to `wss://fstream.asterdex.com/ws` and REST endpoints
- **429 or 5xx responses**: The gateway backs off automatically, but check your rate limits and credentials
- **CLI input errors**: Run in a real TTY; non-interactive shells disable keyboard shortcuts but the UI still renders

## Contributing

Issues and PRs are welcome. When adding strategies or exchanges, follow the modular patterns in `src/core` and add tests under `tests/`.
