# AppSec Pipeline — Automated SAST/DAST/SCA with Vulnerability Triage

A DevSecOps pipeline designed around production security engineering principles — multi-scanner integration, unified finding normalization, and configurable false-positive suppression.

## Architecture

```
Target App (OWASP Juice Shop)
          │
          ▼
┌─────────────────────────────────┐
│       GitHub Actions CI/CD      │
│  ┌──────────┐ ┌───────────────┐ │
│  │   SAST   │ │      SCA      │ │
│  │  Semgrep │ │     Trivy     │ │
│  │          │ │               │ │
│  └──────────┘ └───────────────┘ │
│  ┌──────────────────────────┐   │
│  │   DAST — OWASP ZAP       │   │
│  │   Baseline + Full Scan   │   │
│  └──────────────────────────┘   │
└─────────────────────────────────┘
          │
          ▼ (JSON reports)
┌─────────────────────────────────┐
│     Python Aggregator           │
│  • Normalize to unified schema  │
│  • Deduplicate findings         │
│  • Apply FP suppression rules   │
│  • CVSS-based severity mapping  │
│  • Generate audit trail         │
└─────────────────────────────────┘
          │
          ▼
┌─────────────────────────────────┐
│     aggregated_report.json      │
│  • Before/after noise metrics   │
│  • CWE-mapped findings          │
│  • Suppression audit log        │
└─────────────────────────────────┘
```

## Stack

| Layer | Tool | Purpose |
|---|---|---|
| Target | OWASP Juice Shop | Intentionally vulnerable Node.js app |
| SAST | Semgrep | Static code analysis (OWASP Top 10 ruleset) |
| SCA | Trivy | Dependency vulnerability scanning |
| DAST | OWASP ZAP | Dynamic runtime scanning |
| Aggregator | Python 3.11 | Normalization + triage engine |
| CI/CD | GitHub Actions | Orchestration |

## Repo Structure

```
appsec-pipeline/
├── app/
|   └── juice-shop/
├── .github/
│   └── workflows/
│       ├── semgrep.yml           # Semgrep static analysis
│       ├── trivy.yml             # Trivy dependency scan
│       └── zap.yml               # OWASP ZAP dynamic scan
├── scanner/
│   ├── aggregator.py             # Core triage engine
│   └── false_positive_rules.yml  # Suppression rules (auditable)
├── reports/                      # Generated scan outputs (gitignored)
└── docs/
    └── architecture.md
```

## Quick Start

```bash
# 1. Clone and enter the repo
git clone https://github.com/YOUR_USERNAME/appsec-pipeline
cd appsec-pipeline

# 2. Start Juice Shop locally
docker run -d -p 3000:3000 bkimminich/juice-shop

# 3. Install aggregator dependencies
pip install pyyaml

# 4. Run aggregator against existing reports
python scanner/aggregator.py
```



