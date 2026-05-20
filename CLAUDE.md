# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Status

**End of Life as of August 1, 2020.** No new versions, enhancements, bug fixes, or security updates are planned. The project is seeking community maintainers.

## Repository Purpose

Enterprise MySQL monitoring plugins for three platforms:
- **Nagios** — shell-based check scripts (`nagios/bin/pmp-check-*`)
- **Cacti** — PHP data collectors (`cacti/scripts/ss_get_*.php`)
- **Zabbix** — Python utilities and templates (`zabbix/`)

Primary focus is MySQL, with secondary support for AWS RDS, MongoDB, LVM snapshots, and Unix memory.

## Commands

### Run all Nagios tests
```bash
cd t/nagios && sh check_all.sh
```

### Run tests for a single Nagios plugin
```bash
cd t/nagios/pmp-check-mysql-status && sh check-functions.sh
```

### Build release tarball
```bash
bash make.sh
```
Outputs to `release/` directory. Substitutes `$RELEASE$` and `$PROJECT_NAME$` macros in source files.

### Build packages (RPM/DEB)
```bash
bash build/build.sh
```

## Architecture

### Nagios plugins (`nagios/bin/`)
Shell scripts following Nagios plugin conventions (exit 0=OK, 1=WARNING, 2=CRITICAL, 3=UNKNOWN). Each plugin sources shared functions from itself (functions defined inline, sourced by tests). Plugins accept standard `-w`/`-c` warning/critical thresholds.

Key plugin: `pmp-check-mysql-status` — general-purpose MySQL status variable checker. Supports arithmetic operations on variables (`-x var -o / -y var`), percentage mode (`-T pct`), and threshold comparisons.

Python plugins: `pmp-check-aws-rds.py` (boto/CloudWatch), `pmp-check-mongo.py`.

### Cacti scripts (`cacti/scripts/`)
PHP scripts that output tab-separated key:value pairs consumed by Cacti's data input methods. `ss_get_mysql_stats.php` (~60KB) is the main MySQL stats collector. `ss_get_by_ssh.php` (~65KB) collects stats from remote hosts via SSH.

### Tests (`t/nagios/`)
One directory per plugin, each containing `check-functions.sh` (sources the plugin and tests internal functions) and `samples/` (fixture files with MySQL status variable dumps).

### Documentation (`docs/`)
reStructuredText source files. `util/pod2rst` converts POD from bash scripts to RST. `make.sh` builds HTML docs using Sphinx.

### Versioning
Single `VERSION` file at root. `make.sh` substitutes `$RELEASE$` macro across all source files during release build.
