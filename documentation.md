# Academic Transfer Planner - Documentation

## Project Overview
The Academic Transfer Planner is a single-page web application designed for students at the **Ilia State University (Iliauni) School of Technology (BTE)**. It evaluates a student's academic history, including completed, failed, and in-progress courses, and dynamically simulates credit transfers into alternative bachelor programs offered by the faculty. 

The application maps only successfully completed courses to corresponding equivalents in a chosen target program based on curricular rules.

---

## Architecture and Design Decisions

### 1. Unified Structure (Single-File Philosophy)
To satisfy the project constraints, the application uses an all-in-one approach where structure, appearance, and logic live inside a single `.html` file.
* **HTML5** provides semantic markup (`<header>`, `<main>`, `<section>`, `<table>`, `<select>`).
* **CSS3** delivers an interface aligned with university platforms. It utilizes CSS custom properties (`:root`) for color scaling, responsive grid layouts via media queries, and clean transitions.
* **Vanilla JavaScript (ES6+)** operates entirely on the client side without relying on third-party frameworks or build tools, eliminating runtime network overhead.

### 2. UI/UX Elements
* **Automated Load Rendering:** The transcript table generates instantly when the DOM loads, presenting a standard student profile status.
* **Visual Badging:** Distinct color tokens differentiate passed (`Yes` - green) and unpassed/in-progress status (`No` - red) to allow rapid reading.
* **Dynamic Response & Feedback:** The credit equivalency matrix remains hidden until the user clicks **"Transfer"**, preventing user fatigue and focusing attention on the output table via smooth scrolling triggers.

---

## Technical Implementations

### Curriculum Dataset
The dataset mirrors foundational technology tracks at Ilia State University. The current program is configured as **Civil Engineering**, with the following course data structures:
* **Transcript Courses:** Includes a blend of math streams (Calculus I & II, Linear Algebra), programming basics, and field staples (*Engineering Mechanics: Statics*, *Introduction to Geomatics & Surveying*), with failed/in-progress markers assigned to chemistry and physics pipelines.
* **Curriculum Planning Additions:** Five core Civil Engineering program courses (*Structural Mechanics*, *Fluid Mechanics*, *Soil Mechanics & Foundations*, *Reinforced Concrete Structures*, *Transportation Engineering*) appended to the bottom and locked to a passed status (`Yes`).

### Target Programs and Dropdown Selections
To fulfill the requirement of rendering 3 target programs while excluding the student's own, the dropdown populates the following BTE options:
1.  `Computer Science`
2.  `Architecture`
3.  `Mathematics`

---

## Description of the Equivalency Algorithm

The application relies on a deterministic, rule-based algorithm to map courses between the source program and the target program. The logic prevents academic errors by filtering out unearned credits.

### The Algorithm Pipeline
1. **Conditional Filtering Layer:** When the evaluation is triggered, the engine executes a strict array filter over the master course list (`allCurrentCourses.filter(item => item.passed === "Yes")`). Any course marked as "No" (failed or in-progress) is immediately discarded from the calculation block. Equivalency applies *only* to passed courses.
2. **Target Dictionary Resolution:** The algorithm captures the user's dropdown selection to isolate the target major's sub-dictionary within a master nested object (`equivalencyRules`).
3. **Lookup & Mapping Fallback:** The program iterates through the filtered list of passed courses and performs an $O(1)$ lookup against the target major's rules:
    * **Match:** If a structural mapping exists (e.g., *Engineering Mechanics: Statics* maps to *Mechanics of Architectural Structures* in Architecture), it outputs the target program's equivalent course.
    * **No Match / Distinct Course:** If a course is hyper-specific to Civil Engineering without a direct conversion pathway (e.g., *Fluid Mechanics* moving to Computer Science), the dictionary returns `"N/A"`. The algorithm safely defaults to outputting an italicized gray `"N/A"`, indicating the credit drops into an unrestricted elective pool rather than fulfilling a core requirement.

---

## Improvement Plan & Future Developments: Automatic Syllabus Comparison

The current application relies on a static, hardcoded dictionary (`equivalencyRules`) to determine credit transfers. The most significant future improvement for this software is the integration of an **AI-driven Automatic Syllabus Comparison Algorithm**. 

Instead of manual data entry, this future update will load the official syllabi of academic programs, automatically compare them, and generate the equivalency table dynamically. 

### The AI Syllabus Comparison Algorithm

1. **Document Ingestion & Parsing (OCR & NLP):**
   The system will accept uploaded course syllabi (PDF/DOCX). Optical Character Recognition (OCR) and Natural Language Processing (NLP) tools will extract the raw text and structure it into standardized data points:
   * Course Title and Code
   * ECTS Credit Weight
   * Week-by-Week Topics
   * Measurable Learning Outcomes

2. **Semantic Vectorization:**
   Traditional keyword-matching is flawed because different departments use different terminology (e.g., *Intro to Programming* vs. *CS50*). To solve this, a Large Language Model (LLM) will convert the learning outcomes and weekly topics into high-dimensional vector embeddings. 

3. **Cosine Similarity Calculation:**
   The algorithm will calculate the mathematical distance (Cosine Similarity) between the vector embeddings of the source course and the target course. 
   * If the semantic overlap score is **> 80%**, the courses are flagged as potential equivalents.
   * If the score is **< 80%**, they are marked as non-equivalent (`"N/A"`).

4. **Multi-Agent Verification Panel:**
   Before finalizing a match, AI agents will verify strict academic constraints:
   * **Credit Auditor:** Ensures the ECTS weight of the source course meets or exceeds the target.
   * **Bloom’s Taxonomy Check:** Ensures the cognitive rigor matches (e.g., verifying that a basic "Identification" course doesn't replace an advanced "Synthesis/Design" course).

5. **Dynamic Output Generation:**
   Once verified, the AI automatically outputs the matched courses as a structured JSON object. This data is injected directly into the application's runtime matrix, instantly producing an accurate, up-to-date equivalency table without human intervention.