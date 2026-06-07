# Tool Setup & Configuration Guide

**Document ID:** TOL-001
**Version:** 1.1
**Last Updated:** 2026-06-07
**Status:** Approved

---

## 1. Essential Tools

### 1.1 oXygen XML Editor (DITA Authoring)

**Purpose:** Write and edit DITA topics

**Cost:** ~$4,000-8,000/year for team license (50+ users)

**Setup:**
1. Download oXygen XML Editor
2. Install DITA framework
3. Configure project
4. Setup validation rules
5. Test authoring

**Key Features:**
- DITA authoring interface
- Real-time validation
- Publishing capabilities
- Team collaboration
- Version control integration (Git)

**Configuration Checklist:**
- [ ] Install latest version
- [ ] Enable DITA framework
- [ ] Configure validation rules
- [ ] Setup Git integration
- [ ] Create custom templates
- [ ] Configure metadata defaults

### 1.2 Version Control System (Source Management)

**Purpose:** Store and manage DITA files with change history

**Options:**
| Platform | Type | Typical Cost |
|----------|------|-------------|
| Git-based (on-premises) | Self-hosted | Free (Git server) |
| Git-based (cloud) | Managed | Free to ~$500/user/year |
| SVN / Subversion | Centralized | Free |
| Perforce (Helix Core) | Centralized | ~$500-1,000/user/year |

**Setup Checklist:**
- [ ] Create repository
- [ ] Configure branch protection rules
- [ ] Setup team permissions
- [ ] Enable 2FA for all users
- [ ] Configure backup strategy
- [ ] Install Git client on team machines
- [ ] Setup SSH keys or credential manager

**Key Features:**
- Version control with full history
- Access control and permissions
- Audit logging
- Branching and merging
- Content review workflow
- CI/CD integration

**Repository Structure:**
```
dita-content/
├── src/                      (DITA source files)
│   ├── shared/               (Reusable content library)
│   ├── product-a/
│   ├── product-b/
│   └── images/
├── references/               (Shared variables, legal, keys)
├── output/                   (Generated outputs — gitignored)
├── scripts/                  (Build and validation scripts)
├── build-config.xml          (DITA-OT build configuration)
└── README.md
```

### 1.3 DITA Open Toolkit (Publishing Engine)

**Purpose:** Transform DITA to HTML, PDF, Mobile

**Cost:** FREE (open source)

**Installation:**
```bash
# Download latest version — check https://www.dita-ot.org/ for current release
wget https://github.com/dita-ot/dita-ot/releases/download/4.0/dita-ot-4.0.zip

# Extract
unzip dita-ot-4.0.zip

# Install
cd dita-ot-4.0
./bin/dita install

# Verify
./bin/dita --version
```

**Configuration:**
- [ ] Install DITA-OT 4.x
- [ ] Install PDF plugin (com.elovirta.pdf or org.dita.pdf2)
- [ ] Install HTML5 plugin (bundled)
- [ ] Configure output directories
- [ ] Test transformations on sample content

### 1.4 Translation Management System (Localization)

**Options:**
- **memoQ** (~$10,000-15,000/year)
- **Trados** (~$15,000-20,000/year)
- **Smartling** (cloud-based, pay-per-word)

**Key Features:**
- Translation workflow
- Translation memory
- Terminology management
- Multi-language support
- Cost tracking

**Setup:**
- [ ] Install TMS
- [ ] Configure languages
- [ ] Setup translation memory
- [ ] Configure workflows
- [ ] Train translators
- [ ] Test extraction

### 1.5 CI/CD Pipeline

**Purpose:** Automate publishing on content changes

**Cost:** Varies by platform (see [CI/CD module](../CI-CD/01-CICD-PIPELINE.md))

**Setup:**
1. Choose a CI/CD platform (see platform comparison in the CI/CD module)
2. Configure build triggers (push to main, pull request, scheduled)
3. Define validation steps (XML check, metadata, link validation)
4. Define build steps (DITA-OT multi-format transforms)
5. Define deployment steps (copy to web server, document portal, or network share)

For complete pipeline design, example build scripts, and SLA targets, refer to the dedicated [CI/CD Pipeline document](../CI-CD/01-CICD-PIPELINE.md).

**Integration Checklist:**
- [ ] CI/CD platform selected and configured
- [ ] Build triggers defined (push to main, PR, schedule)
- [ ] Validation stage configured (XML, metadata, links)
- [ ] DITA-OT build commands tested
- [ ] Multi-format output verified
- [ ] Deployment target reachable
- [ ] Notification channel configured

### 1.6 Search Engine

**Purpose:** Full-text search of published documentation

**Options:**
- **Elasticsearch** — open source, self-hosted or managed
- **Algolia** — cloud-based, SaaS
- **Built-in web server search** — basic, no extra cost

**Setup:**
- [ ] Install search engine
- [ ] Configure indices
- [ ] Index HTML documentation
- [ ] Setup search interface
- [ ] Configure multi-language support

---

## 2. Installation Checklist

### Week 1: Foundation
- [ ] Setup repository with branch protection
- [ ] Enable 2FA for all users
- [ ] Create initial folder structure
- [ ] Install Git client on team machines

### Week 2: Authoring Tools
- [ ] Install oXygen XML Editor on team machines
- [ ] Download and extract DITA-OT
- [ ] Install and configure PDF plugin
- [ ] Setup SSH keys or credential manager

### Week 3: Automation
- [ ] Configure CI/CD pipeline
- [ ] Test automated publishing
- [ ] Verify HTML5 output
- [ ] Verify PDF output

### Week 4: Advanced
- [ ] Setup TMS (memoQ/Trados)
- [ ] Configure translation workflows
- [ ] Install search engine
- [ ] Index sample content

---

## 3. Security Best Practices

**Version Control Security:**
- ✅ Enable 2FA (Two-Factor Authentication) for all accounts
- ✅ Use SSH keys (not passwords)
- ✅ Require branch protection (no direct pushes to main)
- ✅ Require review (1+ approvals before merge)
- ✅ Enable audit logging
- ✅ Regular access reviews (quarterly)

**File Security:**
- ✅ No passwords in documentation
- ✅ No API keys in files
- ✅ No sensitive customer data
- ✅ Encrypt TMS credentials
- ✅ Regular security audits

---

## 4. Backup & Recovery

### 4.1 Backup Strategy

| Component | Backup Method | Frequency | Retention |
|-----------|--------------|-----------|-----------|
| Repository | Git clone --mirror | Daily | 30 days |
| DITA-OT installation | Re-downloadable | On setup | N/A |
| TMS database | Vendor-managed | N/A | N/A |
| Build outputs | Rebuild from source | On demand | N/A |

### 4.2 Repository Backup

```bash
# Clone repository with full history
git clone --mirror <repository-url> repo-backup.git

# Backup locally
cp -r repo-backup.git /backup/location/$(date +%Y%m%d)
```

### 4.3 Recovery Process

1. Identify what was lost
2. Checkout previous commit
3. Create new branch from backup
4. Merge back to main

---

## Related Documents

| Document | Location |
|----------|----------|
| CI/CD Pipeline | [CI-CD/01-CICD-PIPELINE.md](../CI-CD/01-CICD-PIPELINE.md) |
| Governance Charter | [GOVERNANCE/01-DITA-GOVERNANCE-CHARTER.md](../GOVERNANCE/01-DITA-GOVERNANCE-CHARTER.md) |
| Onboarding Guide | [TRAINING/01-ONBOARDING-GUIDE.md](../TRAINING/01-ONBOARDING-GUIDE.md) |

**Next Review Date:** Q3 2026  
