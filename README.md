# Day Trader Skill

**A collection of 11 Claude skills for researching, planning, and reviewing day trades.**

Scan thousands of charts for setups, monitor breaking news, time entries, plan exits, calculate position sizes, enforce risk limits, and review possible outcomes before placing a trade.

- **License:** MIT
- **Requirements:** Python 3.8+
- **Dependencies:** Python standard library for the included command-line tools
- **Default trading mode:** Paper trading
- **Data sources:** Public market and government data endpoints

> **Important:** This project is a research and workflow tool, not financial advice. Simulations and historical results do not predict future performance. Read [Risk and limitations](#risk-and-limitations) before using it.

Looking for the original Trader skill? Portfolio tools such as 13F copying, stock screening, tax lots, and rebalancing are in the [Trader skill repository](https://github.com/Jakeschincariol/trader-skill). This repository is its day-trading sequel.

## Contents

- [Features](#features)
- [The 11 skills](#the-11-skills)
- [Installation](#installation)
- [Configuration](#configuration)
- [Typical workflow](#typical-workflow)
- [Command-line examples](#command-line-examples)
- [Tests](#tests)
- [Project structure](#project-structure)
- [Risk and limitations](#risk-and-limitations)
- [License](#license)

## Features

- **Market scanning:** Find opening-range breakouts, flags, oversold bounces, and other configured setups.
- **News monitoring:** Track headlines, SEC filings, and stock-specific developments.
- **Entry and exit planning:** Monitor setups and define stop-loss and target levels.
- **Historical review and simulation:** Replay possible outcomes using recent intraday bars and prior occurrences of a setup.
- **Position sizing and risk checks:** Calculate share size and check orders against configured limits.
- **Alerts:** Monitor stops, targets, and end-of-day exit times.
- **Fundamental and disclosure research:** Review earnings data and public congressional stock-trade disclosures.
- **Human approval:** The workflow is designed to require explicit confirmation before an order is submitted.

The included tools use public data sources such as Yahoo Finance chart data, Nasdaq Trader symbol lists, SEC EDGAR, and the House Clerk. Availability, freshness, and rate limits depend on those providers.

## The 11 skills

| Command | Name | Purpose |
|---|---|---|
| `/trader-scanner` | Chart Scanner | Scans a broad stock universe for setups such as opening-range breakouts, flags, and oversold bounces. |
| `/trader-news` | News Monitor | Tracks breaking news and relevant developments for watched stocks. |
| `/trader-entry` | Entry Timer | Checks a setup and monitors bars for a potential entry trigger. |
| `/trader-exit` | Exit Planner | Defines stop-loss, target, and end-of-day exit levels before entry. |
| `/trader-timemachine` | Time Machine | Simulates possible session outcomes and reviews previous occurrences of the setup. |
| `/trader-risk` | Risk Manager | Checks proposed orders against configured trading and account limits. |
| `/trader-budget` | Budget Manager | Calculates position size from account budget and maximum planned loss. |
| `/trader-broker` | Order Executor | Presents an order for review and can submit it through a configured brokerage connector after approval. |
| `/trader-alerts` | Price Alerts | Checks price levels, stops, targets, and end-of-day alerts. |
| `/trader-analyst` | Earnings Analyst | Retrieves and summarizes company financial facts and earnings-related metrics. |
| `/trader-pelosi` | Pelosi Tracker | Reads publicly filed congressional stock-trade disclosures. |

## Installation

### Option 1: Install through Claude

Paste the following into Claude and ask it to install the skill:

```text
https://github.com/Jakeschincariol/day-trader-skill

install skill
```

### Option 2: Install as a Claude Code plugin

In Claude Code, run:

```text
/plugin marketplace add Jakeschincariol/day-trader-skill
/plugin install day-trader-skill@day-trader-skill
```

When installed as a plugin, the commands are namespaced, for example:

```text
/day-trader-skill:trader-scanner
```

### Option 3: Install the skill folders manually

Clone the repository:

```bash
git clone https://github.com/Jakeschincariol/day-trader-skill.git
cd day-trader-skill
```

Copy the skill folders into your user-level Claude skills directory:

```bash
cp -r trader-* ~/.claude/skills/
```

For a project-specific installation, copy the same folders into your project's `.claude/skills/` directory instead.

> The copy command above uses a Unix-style shell. On Windows, copy the `trader-*` directories into the corresponding Claude skills folder using File Explorer or an equivalent PowerShell command.

### Option 4: Use a skill in a regular chat

You can paste an individual `SKILL.md` file into a chat to use its instructions as a mode. This does not provide the same command-line Python tools as a full installation.

## Requirements

- Python **3.8 or newer**
- No third-party Python packages are required for the included command-line tools.
- Internet access is needed for tools that retrieve public data.
- Some SEC-related tools require a descriptive user agent containing your name and email address.

## Configuration

### SEC user agent

For SEC filing and company-data tools, identify your requests with your name and email address. Configure this in `trader/config.json`:

```json
{
  "sec_user_agent": "Your Name you@example.com"
}
```

Alternatively, set the `SEC_USER_AGENT` environment variable.

Use your own contact details; do not copy the example value unchanged.

### Trading and broker settings

- Review the relevant example configuration files before running the tools.
- Paper trading is the default in the example configuration.
- Keep broker credentials in environment variables or the appropriate secure credential store. **Do not paste API keys or secrets into chat or commit them to Git.**
- Configure your brokerage connector to require approval for each order. Do not set it to always allow trading actions.

Configuration files and supported settings may vary by skill. Check each skill's `SKILL.md` and its example configuration before use.

## Typical workflow

The skills share outputs through files in the `trader/` directory. A typical trade-review sequence is:

```text
Scanner
  -> News
  -> Entry
  -> Exit plan
  -> Time Machine
  -> Budget
  -> Risk check
  -> Broker review
  -> Alerts while the trade is open
```

The analyst and congressional-disclosure tools can be used separately or on a schedule.

### Example workflow

The repository's example describes a hypothetical workflow using historical TSLA data:

1. **Scan:** Find a possible opening-range breakout.
2. **Review news:** Check headlines and relevant filings.
3. **Check entry:** Wait for the configured entry trigger.
4. **Plan the exit:** Set the stop, target, and end-of-day exit rule.
5. **Review possible outcomes:** Run the Time Machine simulation and compare it with historical occurrences of the setup.
6. **Size the position:** Calculate the number of shares that fits the configured maximum loss.
7. **Check risk:** Confirm that the proposed order meets configured limits.
8. **Review the order:** Inspect the order ticket and explicitly approve it before submission.
9. **Monitor:** Check alerts for stop, target, or end-of-day exit conditions.

Simulation outputs are estimates based on the data and assumptions used. They are not guarantees of what will happen in a live market.

## Command-line examples

Run commands from the repository root. Depending on your environment, use `python` instead of `python3`.

### Market scanner

```bash
python3 trader-scanner/scan.py universe
python3 trader-scanner/scan.py daily
python3 trader-scanner/scan.py orb --at 10:15
```

### News monitor

```bash
python3 trader-news/news.py --tickers TSLA --hours 12
```

### Entry timer

```bash
python3 trader-entry/entry.py check TSLA --setup orb
python3 trader-entry/entry.py watch TSLA --every 30
```

### Exit planner and simulation

```bash
python3 trader-exit/exit.py plan --stop or-mid --target 1R --write
python3 trader-timemachine/timemachine.py run --write
```

### Position sizing and risk checks

```bash
python3 trader-budget/size.py --write
python3 trader-risk/check.py
python3 trader-broker/ticket.py show
```

### Alerts

```bash
python3 trader-alerts/alerts.py add --from-plan
python3 trader-alerts/alerts.py check
```

### Company and congressional-trade research

```bash
python3 trader-analyst/facts.py TSLA
python3 trader-pelosi/ptr.py index --member pelosi
```

These examples illustrate the command interfaces documented by the project. Check each skill's `SKILL.md` for supported arguments and configuration.

## Tests

Run the test suite from the repository root:

```bash
python3 -m unittest discover -s tests -v
```

The tests are designed to run offline using fixtures based on captured market data, news, public filings, and company facts. They test the included logic without requiring live market-data requests for every test.

## Project structure

```text
day-trader-skill/
├── .claude-plugin/       # Claude Code plugin metadata
├── trader-scanner/       # Chart scanning
├── trader-news/          # News and filing monitor
├── trader-entry/         # Entry checks and monitoring
├── trader-exit/          # Stop and target planning
├── trader-timemachine/   # Historical simulation
├── trader-risk/          # Order risk checks
├── trader-budget/        # Position sizing
├── trader-broker/        # Order ticket and broker workflow
├── trader-alerts/        # Price and time alerts
├── trader-analyst/       # Company financial facts
├── trader-pelosi/        # Congressional disclosure tracker
├── tests/                # Unit tests and offline fixtures
├── LICENSE
└── README.md
```

Each skill directory contains a `SKILL.md` and its associated Python tool(s). Some skills also include example configuration files.

## Risk and limitations

**This software is not financial advice.** Day trading involves substantial risk, and you can lose money. Consider consulting a licensed financial professional if you need advice about your circumstances.

- **No guaranteed outcomes:** Historical performance and simulations cannot predict future market behavior.
- **Simulation limits:** The Time Machine uses recent historical bars and prior setup occurrences. Unexpected events, halts, earnings, headlines, slippage, and changing market conditions may not be represented.
- **Stops are not guarantees:** In fast markets or across price gaps, a stop order may execute at a worse price than requested. The displayed planned loss is calculated using the stop price.
- **Data limitations:** Public endpoints can be delayed, changed, or rate-limited. The scanner and entry timer use bar data rather than every market tick. Verify important information with your broker.
- **Order approval:** The intended workflow requires the risk check to pass and explicit user approval before an order is submitted. Keep brokerage tool permissions set to require approval for each action.
- **Paper trading first:** Use paper trading while learning the workflow and validating configuration. Do not assume simulated results will match live fills.
- **Trading restrictions:** Broker rules and regulations can change and may differ by account and jurisdiction. Verify current requirements with your broker and relevant regulators.
- **Congressional disclosures:** These are public filings, not inside information, and may be reported after a trade has occurred.
- **Taxes:** Frequent trading can have tax consequences, including possible wash-sale issues. This project does not provide tax advice.

## License

This project is distributed under the [MIT License](LICENSE).
