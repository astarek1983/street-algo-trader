# Jane Street Electronic Trading Competition (Algorithmic Strategies)

This repository contains a collection of algorithmic trading strategies and a client to interact with a Jane Street–style electronic exchange. Strategies include pennying/market-making, a micro MACD signal, ETF basket arbitrage, ADR conversion arbitrage, and a simple bond market maker.

### Key Features

- Pennying market-making around the NBBO
- Micro-MACD momentum signal per symbol (12/20 EMA spread)
- ETF basket arbitrage scaffolding for XLF vs components (GS, MS, WFC, BOND)
- ADR conversion logic (VALBZ/VALE) with convert orders
- Bond market-making around a fair value
- Simple client harness and test harnesses

### Repository Structure

```
├─ client.py           # Main live client (prod by default)
├─ test.py             # Minimal client focusing on micro MACD (test exchange)
├─ test_etf.py         # ETF conversion experiment client (test exchange)
├─ bondtrade.py        # Bond market-making logic
├─ pennying.py         # Pennying strategy for most symbols
├─ micromacd.py        # Micro MACD helper (12/20 EMA logic)
├─ adrconversion.py    # ADR arbitrage logic (VALBZ/VALE convert)
├─ etf-convert.py      # ETF basket conversion utils/experiments
├─ oldbond.py          # Legacy bond logic
├─ client_test.py      # Helper utilities for testing clients
├─ RNN-DJIA/           # Experimental DJIA RNN notebooks and data (unrelated to client)
├─ LICENSE
└─ README.md
```

### Requirements

- Python 2.7+ or 3.x (code uses `print_function`; sockets/JSON only)
- numpy

Install dependencies:

```bash
pip install numpy
```

### Configuration

Edit the CONFIGURATION section inside the desired client script.

- `team_name`: your exchange team identifier
- `test_mode`: set True for test exchange, False for prod
- `test_exchange_index`: choose 0/1/2 for different test environments

Example snippets:

```python
# client.py (prod by default)
test_mode = False

# test.py (test by default)
test_mode = True
```

### Running

Make the client executable and run in a loop (recommended by the competition scaffold):

```bash
chmod +x client.py
while true; do ./client.py; sleep 1; done
```

Or simply run with Python:

```bash
python client.py
```

For the test clients:

```bash
python test.py
python test_etf.py
```

### Strategy Modules

- `pennying.py`: Places quotes slightly inside the inside market, with risk checks based on current outstanding orders and holdings.
- `micromacd.py` (and inline function in clients): Maintains rolling 12-EMA and 20-EMA on mid-prices; issues small buy/sell signals on crossovers.
- `bondtrade.py`: Quotes around the bond’s fair value and manages inventory; can cancel, add, and track best buy.
- `adrconversion.py`: Arbitrage between `VALBZ` and `VALE` via convert orders; sequences buy/convert/sell when spreads appear.
- `etf-convert.py`: Experiments/utilities for XLF basket arbitrage versus components (GS/MS/WFC/BOND). Marked experimental in `test_etf.py`.

### Notes

- Network protocol: JSON over TCP. The client sends `hello`, `add`, `cancel`, `convert` and handles `book`, `fill`, `ack`, `reject`, `error`.
- Rate control: basic `TIMEOUT` sleeps; adjust for your environment.
- Risk: Holdings and outstanding orders are tracked per symbol to limit exposure.

### Testing/Experiments

- Use `test.py` to observe micro-MACD behavior on the test exchange (prints order/ack/fill counts).
- Use `test_etf.py` to explore ETF conversion logic; the code is scaffolded and partially experimental.
- `client_test.py` includes helper routines for interacting in tests.

### License

This project is licensed under the MIT License. See `LICENSE` for details.
