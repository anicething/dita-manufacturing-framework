# DITA Style Guide

**Document ID:** STY-001  
**Version:** 1.0  
**Last Updated:** 2026-06-06  
**Status:** Approved  

---

## 1. Writing Principles

### 1.1 Core Values

**CLARITY** - Write for global audience
- Use simple, direct language
- Define technical terms
- Avoid idioms and cultural references
- Keep sentences under 20 words

**CONCISENESS** - Respect reader's time
- No unnecessary words
- One idea per sentence
- Remove redundancy
- Cut jargon when possible

**CONSISTENCY** - Uniform voice & terminology
- Use same terms for same concepts
- Maintain parallel structure
- Follow brand voice guidelines
- Use approved glossary terms

**COMPLIANCE** - Meet ISO & legal requirements
- Include required warnings
- Cite safety standards
- Include disclaimers where needed
- Track document history

## 2. Grammar & Language

### 2.1 Tone

**Professional but approachable**
- Not: "Ensure optimal configuration parameters are implemented"
- Use: "Configure the settings for your environment"

**Active voice preferred**
- Not: "The file should be saved by the user"
- Use: "Save the file"

**Second person (you) recommended**
- Not: "Users should restart the application"
- Use: "You can restart the application"

### 2.2 Tense

- **Present tense** for procedures and current states
  - "Click Save"
  - "The system displays the confirmation message"
- **Future tense** for expected outcomes
  - "You will see the results"
  - "The file will be uploaded"
- **Avoid past tense** in procedures
  - Not: "Clicked Save and the file was saved"

### 2.3 Numbers

**Spell out 0-9, use numerals for 10+**
- "You need five minutes"
- "The system supports 25 languages"

**Exceptions (always use numerals):**
- Measurements: "2 GB of RAM"
- Percentages: "50%"
- Versions: "Version 2.1"
- Codes: "Error 404"

## 3. Formatting & Structure

### 3.1 Headings

**Hierarchy (max 3 levels in topics):**
```
Level 1 (H1): <title> - Topic title
Level 2 (H2): <section title> - Major section
Level 3 (H3): <section title> - Subsection
```

**Heading style:**
- Use sentence case (not Title Case)
- "Getting started with the system"
- "Configuring security settings"
- Make them descriptive and specific

### 3.2 Lists

**Bullet lists:** Use for items with no sequence
```
Supported file formats:
- PDF
- Word (.docx)
- Excel (.xlsx)
- CSV
```

**Numbered lists:** Use for steps, procedures, priority
```
To install the software:
1. Download the installer
2. Run the setup wizard
3. Accept the license agreement
4. Choose installation directory
5. Click Finish
```

**Parallel structure required:**
- All items same grammatical form
- Not: "1. Install software, 2. Configuration, 3. Running the app"
- Use: "1. Install software, 2. Configure settings, 3. Run the app"

### 3.3 Tables

**Use for comparisons, specifications, reference data**

```
Product Model | Processor | RAM | Storage | Price
─────────────────────────────────────────────────
Basic         | Dual-core | 4GB | 256GB   | $500
Pro           | Quad-core | 8GB | 512GB   | $800
Enterprise    | Octa-core | 16GB| 1TB     | $1200
```

**Table guidelines:**
- First row = headers (bold)
- Consistent column alignment
- Keep rows concise
- Use for 3+ items to compare

### 3.4 Emphasis

**Bold** - UI elements, important terms
```
Click the **Save** button.
The **API Key** is required.
```

**Italics** - Variables, file names, emphasis
```
Replace *username* with your login
Edit the *config.xml* file
```

**Code/Monospace** - Commands, code, file paths
```
Enter the command: `npm install`
Edit */etc/config/settings.conf*
```

**AVOID ALL CAPS** (except acronyms)
- Not: "DO NOT DELETE THIS FILE"
- Use: "Do not delete this file"

## 4. Common Terminology

### 4.1 Standard Manufacturing Terms

| Concept | Use This | NOT This |
|---------|----------|----------|
| Our product | Product A | System, Software, Application |
| User account | Account | User ID, Login, Credentials |
| Start program | Open | Launch, Activate |
| Stop program | Close | Exit, Shut down |
| Configuration | Settings | Parameters, Options |
| IT administrator | Administrator | Admin, System admin |
| Save work | Save | Store, Commit |
| Equipment failure | Error | Fault, Problem |
| Maintenance schedule | Maintenance | Service, Upkeep |

## 5. Warnings, Cautions & Notes

### 5.1 WARNING (Danger to User/Data)

```
⚠️ WARNING: This action cannot be undone. All data will be 
permanently deleted. Make sure you have a backup before proceeding.
```

**Use for:**
- Destructive actions (delete, format, overwrite)
- Security risks
- Data loss risk
- Safety hazards

### 5.2 CAUTION (Undesired Result Possible)

```
⚠️ CAUTION: Changing these settings may affect system performance. 
Test in a non-production environment first.
```

**Use for:**
- Performance impacts
- Compatibility issues
- Configuration conflicts
- Non-reversible changes

### 5.3 NOTE (Helpful Information)

```
📝 NOTE: You must have Administrator privileges to perform this task.
```

**Use for:**
- Prerequisite information
- Helpful tips
- Related information
- Platform-specific notes

## 6. Writing Procedures (Tasks)

### 6.1 Task Structure

```
Task Title (action verb + object)
│
├─ Short description (1-2 sentences)
├─ Purpose (when would you do this?)
├─ Prerequisites (if any)
├─ Steps (numbered, concise)
├─ Expected Result
├─ Tips/Next Steps (optional)
└─ Related Tasks (links)
```

### 6.2 Step Writing Guidelines

**Start each step with an action verb:**
- Open, Click, Select, Enter, Choose, Right-click, Double-click, Copy, Paste

**Be specific:**
- Not: "Click the button"
- Use: "Click the **Save** button"

**One action per step (usually):**
- Not: "Open the file and select the option"
- Use:
  1. Open the file
  2. Select the option

**Include expected result per step (if not obvious):**
```
1. Click **OK**
   The settings are saved and the dialog closes.
```

## 7. Code & Technical Content

### 7.1 Code Blocks

```xml
<codeblock>
<!-- Example code -->
const api = new ProductAPI({
  apiKey: 'YOUR_API_KEY',
  environment: 'production'
});
</codeblock>
```

### 7.2 Inline Code

Use monospace for:
- Variable names: *apiKey*
- File paths: */etc/config.conf*
- Commands: `npm install`
- Code snippets: `const x = 10`

## 8. Images & Visuals

### 8.1 Image Guidelines

**File format:**
- PNG (preferred) for screenshots
- JPG for photographs
- SVG for diagrams

**Size optimization:**
- Maximum 500 KB per image
- Screenshot: 800x600 minimum
- Use compression tools

**Alt text (mandatory):**
```
<image href="../images/login_screen.png" 
       alt="Login screen with username and password fields"/>
```

## 9. Localization Considerations

### 9.1 Translator-Friendly Writing

**Avoid:**
- Idioms: "piece of cake", "break a leg"
- Puns and wordplay
- Abbreviations without explanation
- Culturally-specific references
- Humor that doesn't translate

**Prefer:**
- Literal language
- Sentence fragments OK: "Click Save. The file is saved."
- Active voice
- Short paragraphs
- Simple word choice

### 9.2 Translation-Friendly Structure

**Avoid long paragraphs**
- Maximum 3-4 sentences per paragraph
- One idea per paragraph
- Short sentences (< 20 words)

**Use consistent terminology**
- Maintain glossary
- Same term = same concept always
- Translation memory reuse

## 10. Quality Checklist

### Before Submitting for Review

- [ ] Metadata complete (all required fields filled)
- [ ] No spelling errors
- [ ] Grammar reviewed
- [ ] No broken links
- [ ] No Lorem ipsum or placeholder text
- [ ] Images optimized and alt-text included
- [ ] No security-sensitive information
- [ ] Follows style guide
- [ ] Topic type appropriate (concept/task/reference)
- [ ] Headings use sentence case
- [ ] Numbered lists only for sequences
- [ ] Code properly formatted
- [ ] Warnings/cautions used appropriately
- [ ] Glossary terms used correctly
- [ ] Conrefs used for shared content
- [ ] Cross-references meaningful
- [ ] No CAPITALIZE for emphasis
- [ ] Consistent terminology throughout
- [ ] Ready for translation (global audience)
- [ ] Complies with ISO requirements

---

**Document ID:** STY-001  
**Next Review Date:** Q3 2026  
