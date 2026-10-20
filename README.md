# qbo-callback

The https redirect URI for the KPI pipeline's `python -m qbo connect` (QuickBooks Online OAuth). Intuit's production
keys need an https redirect URI on a real domain; this GitHub Pages page is that address.

`index.html` does one thing: it sends the browser on to `http://localhost:8765/callback` with the exact query string
Intuit added (`code`, `state`, `realmId`), where `qbo connect` is listening on the same computer and checks `state`
before exchanging the code.

- No analytics, no external scripts, styles, fonts or images. A Content-Security-Policy allows only the one inline
  script (by hash), and `referrer: no-referrer` keeps the query string out of any Referer header.
- The page stores nothing and sends nothing anywhere but localhost. `location.replace` keeps the URL with the code out
  of the browser history.
- GitHub's servers see the request like any web host does. The code they could see works once, for a few minutes,
  only together with the app's client secret, which never leaves the pipeline's `.env`.

If the script line changes, update the `sha256-...` hash in the CSP:

    printf '%s' 'location.replace("http://localhost:8765/callback" + location.search);' | openssl dgst -sha256 -binary | base64
