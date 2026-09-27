# Trading skills

The skills in this folder come from [tradermonty/claude-trading-skills](https://github.com/tradermonty/claude-trading-skills), copied at commit [`1b2d158`](https://github.com/tradermonty/claude-trading-skills/commit/1b2d158574ee2aba8ad9fcf175a733cb862d451e) (2026-09-27). Claude Code loads every `.claude/skills/<name>/SKILL.md` automatically when it works in this repo.

They are MIT-licensed by TraderMonty (see [`LICENSE-claude-trading-skills`](LICENSE-claude-trading-skills)). Like upstream, they are for education and research only, not financial advice.

## Skills

| Skill | What it does | API key |
| --- | --- | --- |
| `market-breadth-analyzer` | Scores market breadth health from 0 to 100 | None (public CSV) |
| `sector-analyst` | Reads sector rotation and market cycle position | None (public CSV; chart images optional) |
| `ftd-detector` | Detects Follow-Through Days on the S&P 500 and Nasdaq | FMP |
| `market-top-detector` | Scores market top probability from 0 to 100 | FMP |
| `ibd-distribution-day-monitor` | Tracks QQQ/SPY distribution days and rates market risk | FMP |
| `vcp-screener` | Screens the S&P 500 for Minervini VCP setups | FMP |
| `canslim-screener` | Screens US stocks with O'Neil's CANSLIM method | FMP |
| `technical-analyst` | Analyzes weekly chart images | None (FMP optional) |
| `position-sizer` | Sizes long positions by risk | None |
| `backtest-expert` | Guides and evaluates strategy backtests | None |
| `economic-calendar-fetcher` | Lists upcoming economic releases | FMP |
| `earnings-calendar` | Lists upcoming earnings dates | FMP |

FMP is [Financial Modeling Prep](https://financialmodelingprep.com/developer/docs). Its free tier (250 requests a day) is enough for most runs. Set `FMP_API_KEY` in your environment or pass `--api-key`.

## Setup

The scripts need Python 3.10 or newer. Install each skill's dependencies with:

```bash
for f in .claude/skills/*/requirements.txt; do pip install -r "$f"; done
```

Most scripts save reports to `reports/` by default, and that folder is git-ignored. `ftd-detector` and `market-breadth-analyzer` save to the current directory instead unless you pass `--output-dir reports/`.

## Changes from upstream

Python code, references and assets are unchanged. Only the Markdown instructions were adapted so their commands work from this repo's root:

- Paths `skills/<name>/…` became `.claude/skills/<name>/…`.
- `vcp-screener`'s examples write to `reports/` instead of into the skill's own `scripts/` folder.

## Tests

Run each skill's tests in a separate pytest process. Several skills share module names such as `scorer.py` and `fmp_client.py`, so one combined run would import the wrong ones.

```bash
pip install pytest
for d in .claude/skills/*/scripts/tests; do python3 -m pytest -q "$d"; done
```

## Updating from upstream

```bash
git clone --depth 1 https://github.com/tradermonty/claude-trading-skills /tmp/cts
for s in $(ls -d .claude/skills/*/ | xargs -n1 basename); do
  rm -rf ".claude/skills/$s" && cp -a "/tmp/cts/skills/$s" ".claude/skills/$s"
done
cp /tmp/cts/LICENSE .claude/skills/LICENSE-claude-trading-skills

# Re-apply the path changes described above
names=$(ls -d .claude/skills/*/ | xargs -n1 basename | paste -sd '|')
find .claude/skills -name '*.md' -not -path '.claude/skills/README.md' -print0 |
  xargs -0 perl -pi -e "s{--output-dir skills/vcp-screener/scripts\b}{--output-dir reports/}g; s{(?<![\w./-])skills/($names)/}{.claude/skills/\$1/}g"
```

Then update the commit link at the top of this file.
