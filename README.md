# Fadenstack apps

The released builds of the Fadenstack apps: the clients people use with their organisation's Fadenstack server.

| App | What it is | Where it runs |
|---|---|---|
| Desktop agent | An assistant on your computer | Windows, Linux |
| Office add-ins | Assistants in Word, Excel, PowerPoint and Outlook | Windows, Microsoft Office |
| Nextcloud app | An assistant in Nextcloud | Nextcloud 31 to 35 |
| Chrome extension | An assistant in the browser | Chrome, through the Chrome Web Store |

More about each app: [fadenstack.com/agents](https://fadenstack.com/agents/).

The apps are free to use. They are not open source, and this repository contains no source code. Their terms of
use are published with their first releases.

## Getting an app

The easiest way is your organisation's Fadenstack server: if an administrator offers an app, the server's **Apps**
page links to its download and shows how to connect it. You can also download a release here.

Each app has its own releases, tagged `desktop-v…`, `office-v…`, `nextcloud-v…` and `chrome-v…`. Pre-releases are
marked as such. A release contains the app's files, `SHA256SUMS` and a software bill of materials (CycloneDX).

### Checking a download

Compare the file's SHA-256 with its line in the release's `SHA256SUMS`:

```sh
sha256sum <file>                        # Linux
shasum -a 256 <file>                    # macOS
Get-FileHash <file> -Algorithm SHA256   # Windows PowerShell
```

## How Fadenstack servers learn about new releases

Once a day, a Fadenstack server reads a signed list of these releases from fadenstack.com. It is the same public file
for every server, and nothing about the server or its apps is sent. The server compares the list with the app versions
in use and tells its administrators. They decide which apps their people are offered.

## Security

To report a vulnerability, see [fadenstack.com/security](https://fadenstack.com/security/).
