# MyIP.foo API Examples

**Code examples and integration guides for [myip.foo](https://myip.foo) - a free, privacy-focused IP lookup API**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![API Status](https://img.shields.io/badge/API-Live-brightgreen)](https://myip.foo/api)
[![No Auth Required](https://img.shields.io/badge/Auth-None%20Required-blue)](https://myip.foo/api-docs)

---

## 🚀 Quick Start

**Get your IP in one line:**

```bash
# Plain text
curl https://myip.foo/plain

# JSON with geolocation
curl https://myip.foo/api
```

**No API key required. No registration needed. 100% free.**

---

## 📚 What is MyIP.foo?

[MyIP.foo](https://myip.foo) is a fast, privacy-focused IP address lookup service built on Cloudflare Workers. It provides:

- ✅ **Dual-stack support** - Detects both IPv4 and IPv6
- ✅ **Geolocation data** - City, country, ISP, timezone, coordinates
- ✅ **No logging** - Your IP is never stored
- ✅ **Free API** - JSON and plain text endpoints
- ✅ **GDPR compliant** - Full privacy compliance
- ✅ **Lightning fast** - <50ms response time (edge computing)

---

## 📖 API Endpoints

### `/plain` - Plain Text

Returns only your IP address as plain text.

**Response:**
```
2001:db8::1
```

**Perfect for:**
- Shell scripts
- CI/CD pipelines
- Docker healthchecks
- Quick IP lookups

---

### `/api` - JSON API

Returns comprehensive IP information in JSON format including connection type detection (VPN/Datacenter/Residential).

**Response:**
```json
{
  "ip": "2001:db8::1",
  "type": "IPv6",
  "location": {
    "country": "NL",
    "city": "Amsterdam",
    "region": "North Holland",
    "postalCode": "1012",
    "timezone": "Europe/Amsterdam",
    "latitude": "52.3740",
    "longitude": "4.8897"
  },
  "network": {
    "asn": 1136,
    "isp": "KPN B.V."
  },
  "cloudflare": {
    "colo": "AMS",
    "ray": "7d4f1a2b3c4d5e6f-AMS"
  }
}
```

---

### `/headers` - HTTP Headers

Returns all HTTP headers sent by your browser.

**Response:**
```json
{
  "headers": {
    "user-agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7)...",
    "accept": "text/html,application/xhtml+xml...",
    "accept-language": "en-US,en;q=0.9",
    "accept-encoding": "gzip, deflate, br"
  }
}
```

**Perfect for:**
- Debugging HTTP headers
- Browser fingerprinting research
- Testing proxy/VPN header leaks

---

### `/user-agent` - User Agent String

Returns your browser's User-Agent string as plain text.

**Response:**
```
Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36
```

**Perfect for:**
- Quick browser identification
- Testing User-Agent spoofing
- CI/CD browser detection

---

### `/api/connection-type` - Connection Type Detection

Detects if your connection is from a VPN, datacenter, or residential network.

**Response:**
```json
{
  "ip": "1.2.3.4",
  "connectionType": "datacenter",
  "provider": "Hetzner Online GmbH",
  "asn": 24940
}
```

**Possible values:**
- `residential` - Home/mobile ISP
- `datacenter` - Hosting provider, VPS
- `vpn` - Known VPN provider (via Tor exit nodes database)
- `unknown` - Unable to determine

**Perfect for:**
- VPN detection
- Fraud prevention
- Traffic analysis

---

## 💻 Code Examples

### Bash / curl

```bash
# Get your IP address
curl https://myip.foo/plain

# Save to variable
MY_IP=$(curl -s https://myip.foo/plain)
echo "My IP: $MY_IP"

# Force IPv4
curl -4 https://myip.foo/plain

# Force IPv6
curl -6 https://myip.foo/plain

# Get JSON data
curl -s https://myip.foo/api | jq '.'

# Extract specific field
curl -s https://myip.foo/api | jq -r '.location.country'

# Get User-Agent
curl https://myip.foo/user-agent

# Get all headers
curl -s https://myip.foo/headers | jq '.'

# Check connection type
curl -s https://myip.foo/api/connection-type | jq '.'
```

---

### Python

```python
import requests

# Get IP as string
ip = requests.get('https://myip.foo/plain').text.strip()
print(f"My IP: {ip}")

# Get full data
data = requests.get('https://myip.foo/api').json()
print(f"IP: {data['ip']}")
print(f"Country: {data['location']['country']}")
print(f"City: {data['location']['city']}")
print(f"ISP: {data['network']['isp']}")
```

**VPN Verification:**
```python
import requests

def check_vpn_status():
    data = requests.get('https://myip.foo/api').json()
    expected_country = "NL"  # Netherlands
    actual_country = data['location']['country']

    if actual_country == expected_country:
        print(f"✅ VPN active: {data['ip']} ({data['location']['city']}, {actual_country})")
    else:
        print(f"⚠️  VPN inactive or wrong location: {actual_country}")

check_vpn_status()
```

---

### JavaScript / Node.js

```javascript
// Using fetch (Node.js 18+)
const response = await fetch('https://myip.foo/api');
const data = await response.json();

console.log(`IP: ${data.ip}`);
console.log(`Location: ${data.location.city}, ${data.location.country}`);
console.log(`ISP: ${data.network.isp}`);

// Plain text
const ip = await fetch('https://myip.foo/plain').then(r => r.text());
console.log(`My IP: ${ip.trim()}`);
```

---

### PHP

```php
<?php
// Get IP as string
$ip = file_get_contents('https://myip.foo/plain');
echo "My IP: " . trim($ip);

// Get JSON data
$data = json_decode(file_get_contents('https://myip.foo/api'), true);
echo "IP: " . $data['ip'] . "\n";
echo "Country: " . $data['location']['country'] . "\n";
echo "City: " . $data['location']['city'] . "\n";
?>
```

---

### Go

```go
package main

import (
    "encoding/json"
    "fmt"
    "io"
    "net/http"
)

type IPData struct {
    IP       string `json:"ip"`
    Location struct {
        Country string `json:"country"`
        City    string `json:"city"`
    } `json:"location"`
}

func main() {
    // Plain text
    resp, _ := http.Get("https://myip.foo/plain")
    body, _ := io.ReadAll(resp.Body)
    fmt.Printf("My IP: %s\n", string(body))

    // JSON
    resp, _ = http.Get("https://myip.foo/api")
    var data IPData
    json.NewDecoder(resp.Body).Decode(&data)
    fmt.Printf("%s from %s, %s\n", data.IP, data.Location.City, data.Location.Country)
}
```

---

### Ruby

```ruby
require 'net/http'
require 'json'

# Get IP as string
ip = Net::HTTP.get(URI('https://myip.foo/plain')).strip
puts "My IP: #{ip}"

# Get JSON data
uri = URI('https://myip.foo/api')
response = Net::HTTP.get(uri)
data = JSON.parse(response)
puts "IP: #{data['ip']}"
puts "Country: #{data['location']['country']}"
```

---

### Rust

```rust
use reqwest;
use serde::Deserialize;

#[derive(Deserialize)]
struct Location {
    country: String,
    city: String,
}

#[derive(Deserialize)]
struct IPData {
    ip: String,
    location: Location,
}

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    // Plain text
    let ip = reqwest::get("https://myip.foo/plain")
        .await?
        .text()
        .await?;
    println!("My IP: {}", ip.trim());

    // JSON
    let data: IPData = reqwest::get("https://myip.foo/api")
        .await?
        .json()
        .await?;
    println!("{} from {}, {}", data.ip, data.location.city, data.location.country);

    Ok(())
}
```

---

## 🛠️ Use Cases

### 1. IP Change Monitor

```bash
#!/bin/bash
# check-ip.sh - Monitor IP changes

IP_FILE="/tmp/current_ip.txt"
CURRENT_IP=$(curl -s https://myip.foo/plain)

if [ -f "$IP_FILE" ]; then
  OLD_IP=$(cat "$IP_FILE")
  if [ "$OLD_IP" != "$CURRENT_IP" ]; then
    echo "IP changed: $OLD_IP → $CURRENT_IP"
    # Send notification here
  fi
fi

echo "$CURRENT_IP" > "$IP_FILE"
```

### 2. GitHub Actions - Get Runner IP

```yaml
name: Check Runner IP
on: [push]
jobs:
  check-ip:
    runs-on: ubuntu-latest
    steps:
      - name: Get GitHub Actions runner IP
        run: |
          IP=$(curl -s https://myip.foo/plain)
          echo "Runner IP: $IP"
          curl -s https://myip.foo/api | jq '.'
```

### 3. Docker Healthcheck

```dockerfile
HEALTHCHECK --interval=30s --timeout=3s \
  CMD curl -f https://myip.foo/plain || exit 1
```

### 4. VPN Connection Checker

```python
import requests
import sys

def verify_vpn(expected_country):
    data = requests.get('https://myip.foo/api').json()
    actual_country = data['location']['country']

    if actual_country == expected_country:
        print(f"✅ VPN connected to {actual_country}")
        sys.exit(0)
    else:
        print(f"❌ VPN failed - Location: {actual_country} (expected {expected_country})")
        sys.exit(1)

# Usage: python vpn-check.py NL
if __name__ == "__main__":
    expected = sys.argv[1] if len(sys.argv) > 1 else "US"
    verify_vpn(expected)
```

### 5. Firewall Whitelist Helper

```bash
#!/bin/bash
# whitelist-current-ip.sh - Add current IP to firewall

CURRENT_IP=$(curl -s https://myip.foo/plain)
echo "Adding $CURRENT_IP to firewall whitelist..."

# AWS Security Group example
aws ec2 authorize-security-group-ingress \
  --group-id sg-xxxxx \
  --protocol tcp \
  --port 22 \
  --cidr "$CURRENT_IP/32"

echo "✅ $CURRENT_IP whitelisted for SSH access"
```

---

## 🔒 Privacy

- ✅ **No logging** - Your IP address is NOT stored
- ✅ **No tracking** - No cookies, no user accounts
- ✅ **GDPR compliant** - Full EU privacy compliance
- ✅ **Open source** - Transparent code

See our [Privacy Policy](https://myip.foo/privacy.html) for details.

---

## 📖 Full Documentation

For complete API documentation, visit [myip.foo/api-docs](https://myip.foo/api-docs.html).

---

## 🤝 Contributing

Found a bug or want to add an example? PRs are welcome!

1. Fork this repository
2. Create your feature branch (`git checkout -b feature/amazing-example`)
3. Commit your changes (`git commit -m 'Add Python async example'`)
4. Push to the branch (`git push origin feature/amazing-example`)
5. Open a Pull Request

---

## 📝 License

MIT License - see [LICENSE](LICENSE) file for details.

---

## 💖 Support

If you find MyIP.foo useful, consider:

- ⭐ Starring this repository
- 🐦 Sharing on social media
- ☕ [Buying us a coffee](https://paypal.me/VirtualOx)

---

## 🔗 Links

- **Website:** [myip.foo](https://myip.foo)
- **API Docs:** [myip.foo/api-docs](https://myip.foo/api-docs.html)
- **About:** [myip.foo/about](https://myip.foo/about.html)
- **Issues:** [GitHub Issues](https://github.com/virtualox/myip-examples/issues)

---

## 🏢 About

MyIP.foo is built by [JustFox](https://justfox.dev) (VirtualOx B.V.), an app development company focused on privacy-first, open-source solutions.

---

**Built with ❤️ by JustFox 🦊**
