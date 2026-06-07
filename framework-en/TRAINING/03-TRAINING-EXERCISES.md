# Training Exercises

**Document ID:** TRN-003  
**Version:** 1.0  
**Last Updated:** 2026-06-07  
**Status:** Approved  

---

## Exercise 1: Create a Concept Topic

**Objective:** Write a concept topic describing a product feature.

**Instructions:**
1. Open the Concept template from `templates/concept.dita`
2. Replace placeholder content with real product information
3. Include at least one `<section>` with a bullet list
4. Add complete metadata (author, product, audience)
5. Validate the XML

**Checklist:**
- [ ] Title is a noun phrase (e.g., "Overview of the Spindle Assembly")
- [ ] First paragraph defines the subject
- [ ] Sections organized logically
- [ ] No procedural steps (those belong in tasks)
- [ ] Metadata complete
- [ ] XML validates

---

## Exercise 2: Create a Task Topic

**Objective:** Write a 5-step task procedure.

**Instructions:**
1. Open the Task template from `templates/task.dita`
2. Write a task with prerequisites, 5 steps, and expected result
3. Each step must be a single action (one sentence, < 20 words)
4. Add a warning `<note type="warning">` where appropriate
5. Validate and review

**Checklist:**
- [ ] Title starts with a verb (e.g., "Install the Drive Belt")
- [ ] Prerequisites listed before steps
- [ ] Each step is one instruction
- [ ] Expected result describes what success looks like
- [ ] Warnings placed before the relevant step
- [ ] XML validates

---

## Exercise 3: Create a Reference Topic

**Objective:** Create a reference table for technical specifications.

**Instructions:**
1. Open the Reference template from `templates/reference.dita`
2. Create a `<table>` with technical specifications
3. Include at least 5 rows of data
4. Add a `<strow>` header row
5. Validate and review

**Checklist:**
- [ ] Title is a noun phrase
- [ ] Table has clear column headers
- [ ] Units of measurement included
- [ ] Data is accurate (verify with product specs)
- [ ] No narrative prose (reference = lookup, not reading)
- [ ] XML validates

---

## Exercise 4: Build a DITA Map

**Objective:** Organize 3 topics into a navigable DITA map.

**Instructions:**
1. Create a new `.ditamap` file
2. Add `<topicref>` entries for your 3 topics
3. Set `<navtitle>` for each topic
4. Create a hierarchy (one main topic, two subtopics)
5. Generate HTML5 output using DITA-OT
6. Verify the output renders correctly

**Checklist:**
- [ ] Map has a title
- [ ] All topicrefs point to existing files
- [ ] Navigation hierarchy is logical
- [ ] HTML5 output renders without errors
- [ ] Published output looks correct

---

**Evaluation:** Complete all 4 exercises to be ready for the [Certification Checklist](04-CERTIFICATION-CHECKLIST.md).
