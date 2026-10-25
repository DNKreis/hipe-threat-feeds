# HiPe threat feed catalog

The list of public threat feeds that [HiPe](https://github.com/DNKreis/HiPe) offers to add in one click: on the
first-run setup screen and through the **Catalog** button of the Threat feeds page.

HiPe reads this file from:

```
https://raw.githubusercontent.com/DNKreis/hipe-threat-feeds/main/threat-feeds.json
```

at most once an hour, and only when an administrator opens the catalog (or completes the first-run setup with the
recommended feeds). When the file cannot be read, HiPe shows the copy bundled with the application instead.

## What a change here does to HiPe instances

- **A new source** appears as *Available* in the catalog of every instance. Nothing is added until an administrator
  picks it.
- **A changed source** (URL, format, category or list) is flagged *Update available* on instances that already have
  it. Nothing changes until an administrator applies it; the source keeps its enabled / disabled state.
- **A removed source** disappears from the catalog. Instances that already have it keep it.

Sources are matched by `name`: never rename one, add a new entry instead.

## Format

[`threat-feeds.json`](threat-feeds.json), checked against [`schema.json`](schema.json) on every push and pull request:

```json
{
  "version": 1,
  "sources": [
    {
      "name": "feodotracker",
      "description": "abuse.ch Feodo Tracker: botnet command and control servers",
      "url": "https://feodotracker.abuse.ch/downloads/ipblocklist.txt",
      "format": "plain",
      "commentPrefix": "#",
      "category": "botnet"
    }
  ]
}
```

| Field | Required | Description |
|---|---|---|
| `name` | yes | Stable identifier, unique, 64 characters at most. Shown in HiPe notes and logs. |
| `url` | yes | HTTP(S) address of the feed. |
| `format` | yes | `plain`: one IP or CIDR per line. `urlHost`: one URL per line, the host is kept when it is an IP literal. |
| `commentPrefix` | no | `plain` only: lines starting with it are ignored (default `#`). |
| `category` | yes | `malware`, `botnet`, `scanner`, `spam`, `brute_force`, `tor_exit` or `other`. |
| `list` | no | `blacklist` (default), `greylist` or `whitelist`. |
| `description` | no | One sentence, 300 characters at most, shown in the catalog. |

## Proposing a source

Open a pull request. A good candidate is:

- public and free to use, with terms that allow automated download;
- maintained, with a low false positive rate;
- served in one of the two formats above, without authentication.

The weekly check opens no issue by itself: look at the *Feed availability* runs to spot a feed that stopped answering.

## Own catalog

An organisation can host its own file (same format) and point HiPe to it with the *Threat feed catalog* setting
(`HIPE_SETTING_THREAT_FEED_CATALOG_URL`), or give it as a local file with `HIPE_THREAT_FEEDS_FILE`.
