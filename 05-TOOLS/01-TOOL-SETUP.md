# Tool Setup & Configuration Guide

**Document ID:** TOOLS-001  
**Version:** 1.0  
**Last Updated:** 2026-06-06  
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

### 1.2 GitHub Enterprise (Version Control)

**Purpose:** Store and manage DITA files

**Cost:** ~$500-1,000/year

**Setup:**
1. Create GitHub organization
2. Create repository
3. Configure branch protection
4. Setup team permissions
5. Enable 2FA for all users

**Key Features:**
- Version control
- Access control
- Audit logging
- Branch protection
- Pull requests
- CI/CD integration

**Repository Structure:**
```
dita-content/
├── src/
│   ├── shared/              (Reusable content)
│   ├── product-a/
│   ├── product-b/
│   ├── product-c/
│   └── images/
├── output/                  (Generated outputs)
├── build.xml                (DITA-OT config)
├── .github/
│   └── workflows/
│       └── publish.yml      (CI/CD pipeline)
└── README.md
```

### 1.3 DITA Open Toolkit (Publishing Engine)

**Purpose:** Transform DITA to HTML, PDF, Mobile

**Cost:** FREE (open source)

**Installation:**
```bash
# Download latest version
wget https://github.com/dita-ot/dita-ot/releases/download/3.7/dita-ot-3.7.zip

# Extract
unzip dita-ot-3.7.zip

# Install
cd dita-ot-3.7
./bin/dita install

# Verify
./bin/dita --version
```

**Configuration:**
- [ ] Install DITA-OT 3.7+
- [ ] Install PDF plugin
- [ ] Install HTML5 plugin
- [ ] Configure output directories
- [ ] Test transformations

### 1.4 Translation Management System (Localization)

**Options:**
- **memoQ** (~$10,000-15,000/year)
- **Trados** (~$15,000-20,000/year)

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

### 1.5 CI/CD Pipeline (GitHub Actions)

**Purpose:** Automate publishing on content changes

**Cost:** FREE (built into GitHub)

**Setup:**
Create `.github/workflows/publish.yml`

```yaml
name: DITA Publish

on:
  push:
    branches: [main]
    paths:
      - 'src/**'

jobs:
  publish:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    
    - name: Setup DITA-OT
      run: |
        wget https://github.com/dita-ot/dita-ot/releases/download/3.7/dita-ot-3.7.zip
        unzip dita-ot-3.7.zip
    
    - name: Publish HTML5
      run: |
        ./dita-ot-3.7/bin/dita \
          -input src/product-a/maps/master.ditamap \
          -format html5 \
          -output output/html5
    
    - name: Publish PDF
      run: |
        ./dita-ot-3.7/bin/dita \
          -input src/product-a/maps/master.ditamap \
          -format pdf \
          -output output/pdf
```

### 1.6 Search Engine (Elasticsearch)

**Purpose:** Full-text search of documentation

**Cost:** FREE (open source) or managed service (~$500-2,000/month)

**Setup:**
- [ ] Install Elasticsearch
- [ ] Configure indices
- [ ] Index HTML documentation
- [ ] Setup search interface
- [ ] Configure multi-language support

---

## 2. Installation Checklist

### Week 1: Foundation
- [ ] Create GitHub organization
- [ ] Setup repository with branch protection
- [ ] Enable 2FA for all users
- [ ] Create initial folder structure

### Week 2: Tools
- [ ] Install oXygen XML Editor on team machines
- [ ] Download and extract DITA-OT
- [ ] Install and configure PDF plugin
- [ ] Setup GitHub SSH keys

### Week 3: Automation
- [ ] Create GitHub Actions workflow
- [ ] Test automated publishing
- [ ] Verify HTML5 output
- [ ] Verify PDF output

### Week 4: Advanced
- [ ] Setup TMS (memoQ/Trados)
- [ ] Configure translation workflows
- [ ] Install Elasticsearch
- [ ] Index sample content

---

## 3. Security Best Practices

**GitHub Security:**
- ✅ Enable 2FA (Two-Factor Authentication)
- ✅ Use SSH keys (not passwords)
- ✅ Require branch protection (no direct pushes to main)
- ✅ Require code review (1+ approvals before merge)
- ✅ Enable audit logging
- ✅ Regular access reviews

**File Security:**
- ✅ No passwords in documentation
- ✅ No API keys in files
- ✅ No sensitive customer data
- ✅ Encrypt TMS credentials
- ✅ Regular security audits

---

## 4. Backup & Recovery

**GitHub Backup:**
- GitHub automatically backs up your repository
- Daily backups retained
- Ability to recover deleted branches (24 hours)

**Local Backup:**
```bash
# Clone full repository with history
git clone --mirror https://github.com/org/repo.git

# Backup locally
cp -r repo.git /backup/location/
```

**Recovery Process:**
1. Identify what was lost
2. Checkout previous commit
3. Create new branch from backup
4. Merge back to main

---

**Document ID:** TOOLS-001  
**Next Review Date:** Q3 2026  
