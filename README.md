# Goliath-the-Cybersecurity-dashboard
Cybersecurity Dashboard. Auf Master-Agenten-Basis. Windows. Android &amp; MAC Unterstützung in Entwicklung


# GOLIATH — Cybersecurity Dashboard

**Local Security Monitoring · Windows Inventory · CVE Intelligence**

GOLIATH is a locally operated cybersecurity dashboard designed to collect, centralize, and review Windows system inventory data within an authorized private network. It combines a lightweight Windows agent, a central dashboard server, and an NVD-based vulnerability lookup to support basic security assessments without relying on a cloud-hosted management platform.

**Developed by:** Daniel Kiebler  
**Documentation version:** 1.0  
**Last updated:** October 13, 2026

---

## Overview

GOLIATH follows a simple **Master–Agent architecture**. Windows agents collect system inventory data and submit reports to a central master. The dashboard presents the collected information and supports preliminary software-to-CVE matching using the public National Vulnerability Database (NVD).

The project supports two master configurations:

- **Windows Master:** A Python/Flask server with a browser-based dashboard.
- **Android Master:** An Android application with an embedded HTTP server and the same dashboard interface, designed to receive reports from Windows agents on the same local network.

The project is intended for personal labs, learning environments, and authorized IT security inventory assessments.

## Key Features

- **System Inventory:** Collects hostnames, IPv4 addresses, operating system information, installed software, versions, publishers, and running processes.
- **Central Dashboard:** Displays reporting devices, inventory metrics, software entries, and process information.
- **CVE Lookup:** Searches the public NVD CVE API and displays available CVSS scores, severity information, descriptions, and references.
- **Network Diagnostics:** Performs basic reachability checks for permitted private IP addresses.
- **Local-First Operation:** Stores inventory reports locally on the master device instead of using a cloud-hosted dashboard.
- **Token-Protected Uploads:** Requires a matching agent token before accepting inventory reports.
- **Android Support:** Enables an Android phone to act as the master for Windows agents on the same Wi-Fi or LAN.
- **Lightweight Deployment:** Uses a PowerShell-based Windows agent without requiring a permanently installed agent service.

## Architecture

```text
         Windows PC 01 ──┐
         Windows PC 02 ──┼──> GOLIATH MASTER
         Windows PC 03 ──┘     │
                               ├── Inventory Storage
                               ├── Dashboard UI
                               ├── Network Diagnostics
                               └── NVD CVE Lookup
```

The master can run on Windows or Android. Agents initiate communication with the master; the master does not poll agents or send remote commands.

**Technology stack**

| Component | Technology |
|---|---|
| Windows Master | Python, Flask |
| Windows Agent | PowerShell, Batch |
| Dashboard UI | HTML, CSS, JavaScript |
| Android Master | Java, Android WebView |
| Inventory Storage | JSON |
| Vulnerability Data | NIST NVD CVE API 2.0 |
| Communication | HTTP, JSON, token authentication |

**Default port:** `8765/TCP`

## Project Components

| Component | Version / Status |
|---|---|
| Windows Dashboard Server | v1.1 |
| Windows Inventory Agent | v1.1 |
| Web Dashboard | Included |
| Android Master Application | v2.0.0 — source code available; device testing pending |

## Quick Start — Windows

**Requirements**

- Windows 10 or Windows 11
- Python 3.11 or newer
- A writable project directory
- Network connectivity to the master
- Outbound HTTPS access for NVD lookups

**1. Start the dashboard server**

Extract the project into a writable directory and run:

```bat
START_DASHBOARD.bat
```

Follow the prompts to configure the agent token. Keep the server window open.

**2. Open the dashboard**

Navigate to:

```text
http://127.0.0.1:8765
```

**3. Submit a Windows inventory report**

Run the agent's interactive starter:

```text
agent\COLLECT_AND_SEND.bat
```

Enter the dashboard URL and the same agent token configured on the master.

For a remote Windows agent, use the master's private LAN address, for example:

```text
http://192.168.1.40:8765
```

Remote LAN operation requires the master to listen on the appropriate network interface and the firewall to permit the necessary traffic. Restrict access to trusted networks.

## Android Master

The Android application is designed to host the dashboard server directly on a phone. Windows agents can submit their reports to the phone over the same local network.

The Android project is provided as source code. **A compiled, signed APK and successful on-device testing are not yet confirmed.** Build the application using Android Studio or the project's configured GitHub Actions workflow.

## Security & Privacy

GOLIATH is designed for controlled, authorized inventory collection.

- Agent uploads require token authentication.
- Inventory reports are validated before storage.
- User-supplied IP diagnostics are restricted to permitted local address ranges.
- The Windows agent runs manually and does not install a persistent service or scheduled task.
- The master does not execute arbitrary commands on agents.
- Inventory data remains on the master device under normal local operation.

**Important security considerations**

- Communication currently uses unencrypted HTTP. Operate only on trusted networks; use properly configured TLS when communication crosses less-trusted networks.
- A Windows master exposed to the LAN may also expose its dashboard and API. Apply restrictive firewall rules and additional access controls where necessary.
- Use a long, unique, randomly generated agent token. Do not publish it in source code, screenshots, or logs.
- Hostnames, installed software, and process information can be sensitive. Collect data only from systems you own or are explicitly authorized to administer.

## Limitations

- CVE matching uses heuristic keyword searches. A matching result does not confirm that a particular system or software version is vulnerable.
- Validate findings against affected-version ranges, CPE data, vendor advisories, and available patches.
- Public NVD API rate limits and request timeouts may cause incomplete results.
- Agents report only when started manually. The dashboard's reported-online status indicates a previously received report, not verified live connectivity.
- A new report replaces the existing inventory for the same hostname and IP combination.
- Storage is limited to the latest 100 reports; individual reports are limited to 2,000 software entries and 2,000 process entries.
- The ESP32-S3 board is optional and is not required for the dashboard or Windows agent functionality. The displayed ESP32-S3 interface section does not represent an implemented hardware integration.

## Project Scope

GOLIATH is an inventory and preliminary vulnerability-review tool. It is **not** a full vulnerability management platform, endpoint detection and response (EDR) solution, penetration-testing framework, or replacement for a professional security assessment.

It does not perform network or port scanning, exploit vulnerabilities, collect credentials, or execute arbitrary remote commands.

## Author

**Daniel Kiebler**  
IT Systems Integration · Cybersecurity · Blue Team

---

*GOLIATH — Local visibility. Structured inventory. Security awareness.*

