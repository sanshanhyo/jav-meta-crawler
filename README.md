# JAV Metadata Crawler

`jav-meta-crawler` is a small Python package for querying public JAV metadata by code. It does not download videos. It tries multiple metadata sources in order and returns the first successful result.

Default provider order:

```text
javlibrary -> jav321 -> javdb -> javbus
```

## Install

```bash
pip install .
```

For browser fallback mode:

```bash
pip install ".[browser]"
playwright install chromium
```

## CLI

```bash
jav-meta SSIS-123
jav-meta SSIS-123 --json
javlibrary SSIS-123 --providers jav321,javdb,javbus --timeout 8 --total-timeout 15
javv FC2-PPV-1234567 -o ./config/javlibrary-option.yml
python -m javlibrary_crawler SSIS-123
```

Useful options:

| Option | Description |
| --- | --- |
| `--json` | Print JSON output |
| `-o, --option` | Load a YAML, JSON, or TOML option file |
| `--providers` | Override provider order, for example `jav321,javdb,javbus` |
| `--timeout` | Per-request timeout in seconds |
| `--total-timeout` | Overall lookup timeout in seconds |
| `--proxy` | HTTP proxy, for example `http://127.0.0.1:7890` |
| `--fetcher` | Fetch mode: `curl`, `http`, or `browser` |

Exit/error codes include:

| Code | Meaning |
| --- | --- |
| `JAV_CODE_INVALID` | Invalid video code |
| `JAV_NOT_FOUND` | No matching metadata found |
| `JAV_SOURCE_BLOCKED` | Source blocked the request |
| `JAV_FETCH_TIMEOUT` | Lookup timed out |
| `JAV_FETCH_FAILED` | Network or fetch failure |
| `JAV_PARSE_FAILED` | Page parsing failure |

## Python API

```python
from javlibrary_crawler import create_option_by_file, lookup

option = create_option_by_file("./config/javlibrary-option.yml")
video = lookup("SSIS-123", option)

print(video.source, video.code, video.title)
print(video.to_dict())
```

## Config

Copy the example config and fill only the values you need:

```bash
cp config/javlibrary-option.yml.example config/javlibrary-option.yml
```

Example:

```yaml
base_url: https://www.javlibrary.com
language: cn
provider_order:
  - javlibrary
  - jav321
  - javdb
  - javbus
javdb_base_url: https://javdb.com
javbus_base_url: https://www.javbus.com
jav321_base_url: https://www.jav321.com
timeout_seconds: 8
total_timeout_seconds: 15
fetcher: curl

request:
  user_agent:
  cookie:
  proxy:
  impersonate: random
  retry_times: 1

browser:
  profile_dir: ../data/javlibrary-browser
  channel:
  headless: false
  wait_seconds: 120
```

You can also use environment variables:

```env
JAVLIBRARY_PROVIDER_ORDER=javlibrary,jav321,javdb,javbus
JAVLIBRARY_TIMEOUT_SECONDS=8
JAVLIBRARY_TOTAL_TIMEOUT_SECONDS=15
JAVLIBRARY_PROXY=
JAVLIBRARY_COOKIE=
JAVLIBRARY_FETCHER=curl
```

Do not commit real cookies, tokens, or browser profiles.

## Development

```bash
pip install -e ".[test]"
pytest
python -m compileall javlibrary_crawler tests
```
