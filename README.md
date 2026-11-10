# Fadenstack apps

The released builds of the Fadenstack apps, the clients people run against their organisation's Fadenstack server:

| App | What it is | Where it runs |
|---|---|---|
| `desktop` | The desktop agent | Windows, Linux (macOS later) |
| `office` | The add-ins for Word, Excel, PowerPoint and Outlook (classic) | Windows, Microsoft Office |
| `nextcloud` | The Fadenstack app for Nextcloud | Nextcloud 31 to 35 |
| `chrome` | The browser extension | Chrome, through the Chrome Web Store |

This repository holds **no source code**. The apps are free to use and closed source; their code lives in private
repositories. What is here: the releases (installers, packages, checksums, SBOMs), the list of them that Fadenstack
servers read (`apps.yaml`, published as `apps.json`), and the terms under which the apps are used.

## Releases

Each app is released under its own tag: `desktop-v0.2.0`, `office-v0.1.0`, `nextcloud-v0.1.10`, `chrome-v0.1.0`.
A release carries the app's files, `SHA256SUMS` and a CycloneDX SBOM. Pre-releases (`0.2.0rc1`) are marked as such.

## The app list (`apps.yaml` → `apps.json`)

Fadenstack servers read `https://fadenstack.com/updates/v1/apps.json` (and `apps.json.sig`) once a day, beside the
release list of Fadenstack itself. The same static file for every server: nothing about a server or its apps is
sent. A server shows its administrators new apps and releases, warns them when an app version people use there has
a security fix, and offers the apps its administrators choose to everyone signed in, with the download links and
how to connect them.

`apps.yaml` is the source. On every change to `main`, the workflow `app-list.yml`:

1. checks out Fadenstack's public repository for the builder (`release/update_feed.py`), which refuses anything
   servers would not show: links outside `fadenstack.com`, `github.com/fadenstack/`, the Chrome Web Store and the
   Nextcloud app store, a file without its SHA-256 and size, a pre-release outside the preview channel, a missing
   English text;
2. signs `apps.json` with the secret `APPS_FEED_SIGNING_KEY` (a key servers trust for this list only, not the
   release list's);
3. puts `apps.json` and `apps.json.sig` on fadenstack.com.

### Releasing an app

1. Publish the release here (from the app's private repository's release job, or by hand):
   `gh release create <app>-v<version> --repo fadenstack/fadenstack-apps --title "..." [--prerelease] <files> SHA256SUMS <sbom>`.
2. Add the release to `apps.yaml` with the URL, SHA-256 and size of each file (newest first is not needed: the
   builder sorts). Set `severity: security` when it fixes a security problem, and list the fixes once they may be
   public.
3. Merge to `main`. Within a day every Fadenstack server that checks for releases knows (at once with "Check now").

The fields are described at the top of `apps.yaml`.

## Before the first release (owner)

- Create this repository on GitHub as **public** (no source in it; release assets of a private repository cannot be
  downloaded by the public).
- Secrets: `APPS_FEED_SIGNING_KEY` (the private key `apps-2026-11`, base64), and for the upload to fadenstack.com
  `FEED_DEPLOY_SSH_KEY`, `FEED_DEPLOY_HOST`, `FEED_DEPLOY_KNOWN_HOSTS` (the same restricted key as core's feed job,
  or a second one limited to the same folder).
- The terms of use (EULA), third-party notices and the privacy notice for each app.
- `SECURITY.md`: the contact for reporting vulnerabilities (Cyber Resilience Act, Art. 14 reporting applies since
  11 September 2026).
- Support periods per app series (`series` in `apps.yaml`): servers show them, and name versions past their end.

## Keys

| Key id | Use | Where |
|---|---|---|
| `fadenstack-apps-2026-11` | Signs `apps.json` | The secret `APPS_FEED_SIGNING_KEY` here; an offline copy |
| `fadenstack-apps-backup-2026-11` | Signs only when the key above is lost or leaked | Offline only |

Servers trust the public halves (`APPS_TRUSTED_KEYS` in Fadenstack's `release_feed.py`). A new key ships in a
Fadenstack release before anything is signed with it. When the signing key is lost or leaked: sign the next list with
the backup key, ship a new pair's public key in a Fadenstack release, then sign with the new key.
