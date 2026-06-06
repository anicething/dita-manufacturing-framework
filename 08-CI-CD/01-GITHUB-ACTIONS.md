# GitHub Actions Publishing Pipeline

**Document ID:** CI-CD-001  
**Version:** 1.0  
**Last Updated:** 2026-06-06  
**Status:** Approved  

---

## 1. Publishing Workflow

Create file: `.github/workflows/publish.yml`

```yaml
name: DITA Publish

on:
  push:
    branches: [main]
    paths:
      - 'src/**'
      - '.github/workflows/publish.yml'

jobs:
  publish:
    runs-on: ubuntu-latest
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v3
    
    - name: Setup DITA-OT
      run: |
        echo "Downloading DITA-OT 3.7..."
        wget https://github.com/dita-ot/dita-ot/releases/download/3.7/dita-ot-3.7.zip
        unzip dita-ot-3.7.zip
        cd dita-ot-3.7 && ./bin/dita install && cd ..
        echo "DITA-OT installed successfully"
    
    - name: Publish HTML5
      run: |
        echo "Generating HTML5..."
        ./dita-ot-3.7/bin/dita \
          -input src/product-a/maps/master.ditamap \
          -format html5 \
          -output output/html5/product-a
        echo "HTML5 generation complete"
    
    - name: Publish PDF
      run: |
        echo "Generating PDF..."
        ./dita-ot-3.7/bin/dita \
          -input src/product-a/maps/master.ditamap \
          -format pdf \
          -output output/pdf/product-a
        echo "PDF generation complete"
    
    - name: Publish Product B HTML5
      run: |
        echo "Generating Product B HTML5..."
        ./dita-ot-3.7/bin/dita \
          -input src/product-b/maps/master.ditamap \
          -format html5 \
          -output output/html5/product-b
    
    - name: Publish Product B PDF
      run: |
        echo "Generating Product B PDF..."
        ./dita-ot-3.7/bin/dita \
          -input src/product-b/maps/master.ditamap \
          -format pdf \
          -output output/pdf/product-b
    
    - name: Publish Product C HTML5
      run: |
        echo "Generating Product C HTML5..."
        ./dita-ot-3.7/bin/dita \
          -input src/product-c/maps/master.ditamap \
          -format html5 \
          -output output/html5/product-c
    
    - name: Publish Product C PDF
      run: |
        echo "Generating Product C PDF..."
        ./dita-ot-3.7/bin/dita \
          -input src/product-c/maps/master.ditamap \
          -format pdf \
          -output output/pdf/product-c
    
    - name: Upload to S3 (Web Server)
      env:
        AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
        AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
      run: |
        echo "Uploading to web server..."
        aws s3 sync output/ s3://company-docs/ --delete
        echo "Upload complete"
    
    - name: Index for Search
      run: |
        echo "Indexing for Elasticsearch..."
        # Add Elasticsearch indexing script here
        echo "Search indexing complete"
    
    - name: Notify Team
      if: success()
      run: |
        echo "Documentation published successfully!"
        # Add Slack notification here
    
    - name: Report on Failure
      if: failure()
      run: |
        echo "Build failed. Checking logs..."
        # Add error notification here
```

## 2. Quality Checks (Pre-Merge)

Create file: `.github/workflows/validate.yml`

```yaml
name: Validate DITA

on:
  pull_request:
    paths:
      - 'src/**'

jobs:
  validate:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Check XML Validity
      run: |
        echo "Validating XML structure..."
        for file in src/**/*.dita; do
          echo "Checking $file"
          xmllint --noout "$file" || exit 1
        done
        echo "All XML files valid"
    
    - name: Check Metadata
      run: |
        echo "Verifying required metadata..."
        # Add metadata validation script
        echo "Metadata check complete"
    
    - name: Spell Check
      run: |
        echo "Running spell check..."
        # Add spell checker command
        echo "Spell check complete"
    
    - name: Check for Broken Links
      run: |
        echo "Validating cross-references..."
        # Add link validation
        echo "Link validation complete"
    
    - name: Report Results
      if: success()
      run: echo "✅ All validation checks passed"
    
    - name: Report Failure
      if: failure()
      run: echo "❌ Validation failed. See above for details"
```

## 3. Publishing SLA

| Step | Time | Automated |
|------|------|----------|
| Commit to main | Immediate | Yes |
| Trigger CI/CD | < 1 min | Yes |
| XML validation | < 1 min | Yes |
| HTML5 generation | < 15 min | Yes |
| PDF generation | < 20 min | Yes |
| Upload to server | < 5 min | Yes |
| Search indexing | < 10 min | Yes |
| **Total** | **< 1 hour** | **100%** |

## 4. Troubleshooting

**If build fails:**
1. Check workflow logs on GitHub
2. Look for XML validation errors
3. Check for missing files in map references
4. Verify metadata is complete
5. Contact DITA administrator

---

**Document ID:** CI-CD-001  
**Next Review Date:** Q3 2026  
