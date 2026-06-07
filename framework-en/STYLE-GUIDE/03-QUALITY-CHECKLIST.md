# Quality Checklist — Pre-Publication Verification

**Document ID:** STY-004  
**Version:** 1.0  
**Last Updated:** 2026-06-07  
**Status:** Approved  

---

## Structure

- [ ] Topic type matches content (concept / task / reference)
- [ ] Title follows type conventions
- [ ] Sections are logically ordered
- [ ] No orphan headings (heading followed by heading with no text)
- [ ] Lists contain 2+ items (or use a single sentence)
- [ ] Table has header row (`<strow>` or `<thead>`)

---

## Metadata

- [ ] `<author>` present
- [ ] `<docid>` matches pattern `DOC-PROD-XXX-vN.N`
- [ ] `<product>` and `<product-version>` present
- [ ] `<audience>` is valid (admin / user / developer / executive)
- [ ] `<status>` is correct (draft / review / approved)
- [ ] `<security-level>` set (public / internal / confidential)
- [ ] `<revised-date>` matches today's date

---

## Writing Quality

- [ ] Each sentence is under 20 words (procedures) or 25 words (concepts)
- [ ] Active voice used throughout
- [ ] No weasel words (easy, simple, just, basically, obviously)
- [ ] One clause per sentence (no semicolons in procedures)
- [ ] Consistent terminology (no synonyms for the same thing)
- [ ] No future tense ("will") — use present tense
- [ ] No directional references ("above", "below") — use cross-references

---

## Task Procedures

- [ ] Title starts with a verb
- [ ] Prerequisites listed before first step
- [ ] Each step is one action
- [ ] Step is under 20 words
- [ ] Expected result stated after last step
- [ ] Warnings placed before the relevant step, not after
- [ ] No hidden assumptions about user knowledge

---

## Links & References

- [ ] All `<xref>` targets exist
- [ ] All `<conref>` targets exist
- [ ] All `<keyref>` keys defined in the map
- [ ] No broken image references
- [ ] External links use HTTPS where available

---

## Images

- [ ] Image resolution adequate (not blurry)
- [ ] Alt text present and descriptive
- [ ] Image file size optimized (< 500 KB)
- [ ] Screenshots show only relevant area
- [ ] No outdated UI shown in screenshot

---

## Localization

- [ ] No culture-specific idioms or metaphors
- [ ] No text embedded in images
- [ ] No ambiguous pronoun references
- [ ] Units of measurement include both metric and imperial where needed
- [ ] Date formats use ISO 8601 (YYYY-MM-DD)

---

## Safety & Compliance

- [ ] Hazard statements use correct signal word (DANGER / WARNING / CAUTION)
- [ ] Safety information precedes the relevant procedure step
- [ ] Legal disclaimers present where required
- [ ] ISO compliance requirements met
- [ ] No confidential information exposed

---

## XML Validation

- [ ] XML well-formed (no unclosed tags)
- [ ] DITA schema validates without errors
- [ ] No DTD resolution warnings
- [ ] Subject scheme validation passed
- [ ] `@class` attribute inheritance chain correct

---

## Pre-Submit Final Check

- [ ] Self-review completed
- [ ] Spelling and grammar checked
- [ ] Preview looks correct in oXygen Author view
- [ ] Ready for SME review

---

**Document ID:** STY-004  
**Next Review Date:** Q3 2026
