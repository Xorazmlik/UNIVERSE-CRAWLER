```
██╗   ██╗███╗   ██╗██╗██╗   ██╗███████╗██████╗ ███████╗███████╗
██║   ██║████╗  ██║██║██║   ██║██╔════╝██╔══██╗██╔════╝██╔════╝
██║   ██║██╔██╗ ██║██║██║   ██║█████╗  ██████╔╝███████╗█████╗  
██║   ██║██║╚██╗██║██║╚██╗ ██╔╝██╔══╝  ██╔══██╗╚════██║██╔══╝  
╚██████╔╝██║ ╚████║██║ ╚████╔╝ ███████╗██║  ██║███████║███████╗
 ╚═════╝ ╚═╝  ╚═══╝╚═╝  ╚═══╝  ╚══════╝╚═╝  ╚═╝╚══════╝╚══════╝
                                                                  
 ██████╗██████╗  █████╗ ██╗    ██╗██╗     ███████╗██████╗        
██╔════╝██╔══██╗██╔══██╗██║    ██║██║     ██╔════╝██╔══██╗       
██║     ██████╔╝███████║██║ █╗ ██║██║     █████╗  ██████╔╝       
██║     ██╔══██╗██╔══██║██║███╗██║██║     ██╔══╝  ██╔══██╗       
╚██████╗██║  ██║██║  ██║╚███╔███╔╝███████╗███████╗██║  ██║       
 ╚═════╝╚═╝  ╚═╝╚═╝  ╚═╝ ╚══╝╚══╝ ╚══════╝╚══════╝╚═╝  ╚═╝       
```

<div align="center">

![Python](https://img.shields.io/badge/Python-3.8%2B-blue?style=for-the-badge&logo=python&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-Required-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![SearxNG](https://img.shields.io/badge/SearxNG-Powered-EF5B25?style=for-the-badge&logo=searxng&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)
![OSINT](https://img.shields.io/badge/Category-OSINT-red?style=for-the-badge)

**An infinite-stream URL harvester powered by a private SearxNG engine.**  
*Built for Red Teamers, OSINT analysts, and threat intelligence researchers.*

</div>

---

## 📖 Table of Contents

- [What is UNIVERSE-CRAWLER?](#-what-is-universe-crawler)
- [Why UNIVERSE-CRAWLER?](#-why-universe-crawler)
- [Architecture](#-architecture)
- [File Structure](#-file-structure)
- [Installation](#-installation)
- [Usage](#-usage)
- [Google Dorking with dork.txt](#-google-dorking-with-dorktxt)
- [Output](#-output)
- [Disclaimer](#-disclaimer)
- [Author](#-author)

---

## 🌌 What is UNIVERSE-CRAWLER?

**UNIVERSE-CRAWLER** is a high-performance OSINT URL harvesting tool that queries multiple public search engines simultaneously through a self-hosted **SearxNG** instance running in Docker. It is designed to collect thousands of URLs from open sources in an infinite loop — until you decide to stop.

Whether you are conducting reconnaissance, building a target dataset, or hunting for exposed files via Google Dorks, UNIVERSE-CRAWLER gives you a clean, terminal-based experience with real-time feedback and automatic result export.

---

## ⚡ Why UNIVERSE-CRAWLER?

| Feature | UNIVERSE-CRAWLER | Generic scrapers |
|---|---|---|
| **Privacy** | Routes through private SearxNG — no API keys, no rate-limit bans | Direct API calls expose your identity |
| **Multi-engine** | 10+ engines simultaneously (Google, Bing, Brave, Yahoo…) | Usually 1–2 engines |
| **Infinite pagination** | Crawls indefinitely until stopped | Fixed page limits |
| **Dork-ready** | Ships with a `dork.txt` file for instant Red Team queries | Manual query building |
| **Zero noise** | Deduplicates results on the fly | Duplicate URLs flood output |
| **Graceful exit** | `Ctrl+C` → auto-saves all URLs to `.txt` | Data loss on interrupt |
| **Self-hosted** | Fully offline-capable after Docker setup | Cloud-dependent |

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────┐
│               search.py (CLI)               │
│         ASCII Banner + ANSI Colors          │
└──────────────────┬──────────────────────────┘
                   │ HTTP Requests
                   ▼
┌─────────────────────────────────────────────┐
│         SearxNG (Docker Container)          │
│         localhost:8080  /search             │
│         Configured via searxng/settings.yml │
└────────┬──────────┬────────┬────────────────┘
         │          │        │
    ┌────▼──┐  ┌────▼──┐ ┌──▼──────┐
    │Google │  │ Bing  │ │  Brave  │  ... 10+ engines
    └───────┘  └───────┘ └─────────┘
```

---

## 📁 File Structure

```
UNIVERSE-CRAWLER/
│
├── search.py              # Main crawler script
├── docker-compose.yaml    # Spins up SearxNG automatically
├── dork.txt               # Pre-built Google Dork query collection
│
└── searxng/
    └── settings.yml       # Custom search engine configuration
```

---

## 🛠️ Installation

### Prerequisites

- [Docker](https://docs.docker.com/get-docker/) + [Docker Compose](https://docs.docker.com/compose/install/)
- Python 3.8+

### Step 1 — Clone the repository

```bash
git clone https://github.com/Xorazmlik/UNIVERSE-CRAWLER.git
cd UNIVERSE-CRAWLER
```

### Step 2 — Launch SearxNG with Docker

```bash
docker compose up -d
```

This starts a private SearxNG instance at `http://localhost:8080`.  
Verify it is running:

```bash
docker ps
# You should see a running searxng container
```

### Step 3 — Install Python dependencies

```bash
pip install requests
```

> **Note:** No virtual environment is strictly required, but it is recommended:
> ```bash
> python3 -m venv venv && source venv/bin/activate
> pip install requests
> ```

---

## 🚀 Usage

### Basic run

```bash
python3 search.py
```

You will be greeted by the ASCII banner, then prompted to enter your search query.

### Example session

```
[*] Enter your query: filetype:env DB_PASSWORD
[*] Starting infinite crawl... Press Ctrl+C to stop and save.

[+] Page 1 — 10 URLs collected
[+] Page 2 — 10 URLs collected
[+] Page 3 — 10 URLs collected
...
^C
[!] Interrupt received.
[?] Save collected URLs to file? (y/n): y
[✓] Saved 312 URLs → results_2025-07-14_03-22-11.txt
```

### Tips

- Use **specific dork queries** for higher-quality results (see [`dork.txt`](#-google-dorking-with-dorktxt)).
- The crawler **auto-deduplicates** — running it longer adds more unique results.
- Results are timestamped automatically so you never overwrite previous runs.

---

## 🎯 Google Dorking with `dork.txt`

The repository ships with `dork.txt`, a curated collection of Google Dork queries optimized for Red Team operations and OSINT reconnaissance.

### How to use it

Open the file and pick a dork:

```bash
cat dork.txt
```

Sample entries you will find inside:

```
filetype:env DB_PASSWORD
filetype:sql "INSERT INTO users"
intitle:"index of" ".git"
intitle:"index of" "id_rsa"
inurl:/wp-content/uploads/ filetype:pdf
site:pastebin.com "password"
inurl:".php?id=" site:.gov
filetype:log username password
```

Then paste the chosen dork directly into the crawler prompt:

```
[*] Enter your query: intitle:"index of" "id_rsa"
```

> ⚠️ **Use responsibly.** Dorking is a passive reconnaissance technique.  
> Only target systems you are explicitly authorized to test.

---

## 📄 Output

All results are saved as plain `.txt` files in the current directory:

```
results_2025-07-14_03-22-11.txt
```

Format — one URL per line:

```
https://example.com/exposed/.env
https://target.org/backup/db_dump.sql
https://site.net/.git/config
...
```

These files are ready to pipe directly into other tools:

```bash
# Feed into httpx for live probing
cat results_*.txt | httpx -silent

# Feed into nuclei for vulnerability scanning
nuclei -l results_2025-07-14_03-22-11.txt -t exposures/
```

---

## ⚠️ Disclaimer

> **UNIVERSE-CRAWLER is intended for legal and authorized security research, OSINT investigations, and educational purposes only.**
>
> The author takes **no responsibility** for any misuse of this tool. Using this software against systems without explicit written permission from the target owner may violate local, national, or international laws. You are solely responsible for your actions.
>
> *With great power comes great responsibility.*

---

## 👤 Author

<div align="center">

```
 ██╗   ██╗██╗██████╗ 
 ██║   ██║██║██╔══██╗
 ██║   ██║██║██████╔╝
 ╚██╗ ██╔╝██║██╔═══╝ 
  ╚████╔╝ ██║██║     
   ╚═══╝  ╚═╝╚═╝     
```

**VIP** — `Universe.exe`

> *"The universe is not made of atoms; it is made of data."*


</div>

---

<div align="center">
<sub>Built with 🖤 for the OSINT & Red Team community · UNIVERSE-CRAWLER © 2025</sub>
</div>
