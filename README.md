# Career Guidance System

An offline, explainable career guidance expert system created and maintained by
**Abhishika** (GitHub: [ika12345](https://github.com/ika12345), NIT Hamirpur).
The application recommends suitable career paths from a student's interests,
skills, favourite subjects, education level and career preferences.

Some datasets and helper components are reused under the MIT License. See
[LICENSE](LICENSE) for the applicable notice.

---

## Overview

The student fills in a profile form. A lightweight **agent controller** passes the profile to a **rule-based inference engine**, which scores 14 supported careers and returns the top 3 with reasons, skill gaps and a learning roadmap.

```
Student Input → Student Profile → Agent Controller → Rule-Based Inference Engine
              → Career Recommendation → Explanation + Skill Gaps + Roadmap
```

Everything runs locally. **No API keys, no paid services, no external AI.**

## Main features

- **Student profile form** — education level, interests, favourite subjects, career preferences, technical and non-technical skills, optional extra skills and optional PDF resume (skills are detected automatically).
- **Top 3 career recommendations** — each with a compatibility percentage, why it was recommended, relevant skills, skills you already have and skills to improve.
- **Expert system** — IF-THEN rules kept in one readable file (`modules/expert_rules.py`).
- **Explainable results** — for every career, the rules that fired, which of your inputs matched them, and the exact score breakdown.
- **Learning roadmap** — four stages per career (Fundamentals → Core skills → Projects and practice → Portfolio and job preparation), plus recommended courses for your skill gaps.

## Technologies used

Python · Streamlit · Pandas / NumPy · pdfplumber (resume parsing) · JSON datasets

## Installation

Python 3.11 or newer is recommended (the project's devcontainer uses 3.11).

```powershell
git clone https://github.com/ika12345/Career_Guidance_system.git
cd Career_Guidance_system
py -3.13 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

On macOS or Linux, use `python3 -m venv .venv` and
`source .venv/bin/activate` instead.

## Run

```powershell
streamlit run app.py
```

The app opens at <http://localhost:8501>.

## Project structure

```
app.py                       Streamlit interface
modules/
  expert_rules.py            Form options, scoring settings and the IF-THEN rules
  inference_engine.py        Rule-based inference engine (fires rules, scores careers)
  agent.py                   Agent controller: perceive -> reason -> act
  helpers.py                 Original helpers (data loading, resume parsing, courses)
data/
  careers.json               Careers and their required skills (original dataset)
  career_info.json           Difficulty and learning time (original dataset)
  courses.json               Course catalogue per skill (original dataset)
  skills.json                Master skill list for resume parsing (original dataset)
  roadmaps.json              4-stage roadmaps for the 14 supported careers (new)
tests/test_expert_system.py  Automated checks for the expert system
```

## How the expert system makes recommendations

1. **Rules.** Each rule has conditions on interests, skills, subjects, preferences or education. A condition is met if the student has *at least one* of the listed values; a rule **fires** only if *all* its conditions are met. Example:

   | Rule | IF | THEN |
   |---|---|---|
   | SD1 | interest *Programming & software development* **and** a coding skill (Python, Java, C++ …) | + 40 points for **Software Developer** |
   | DA1 | interest *Data analysis & statistics* **and** subject *Mathematics* or *Statistics* | + 40 points for **Data Analyst** |
   | UX1 | interest *Design & creativity* **and** a design skill (Creativity, Figma …) | + 40 points for **UI UX Designer** |

   The rules of each career add up to 100 points.
2. **Skill overlap.** The student's skills are compared with the skills the career needs in `data/careers.json` (matching ignores upper/lower case). Having 8 or more of them gives the full skill score.
3. **Compatibility** = 60 % × rule score + 40 % × skill score. Careers below 10 % are not recommended, and the best three are shown.
4. **Explanation.** The app lists the fired rules, the student inputs that triggered them, the score breakdown, and the missing skills (the skill gaps).

The same input always gives the same result — nothing is random.

## Demo scenarios

Choose these in the form (education level: Undergraduate unless stated):

| Profile | Inputs | Top result |
|---|---|---|
| Aspiring developer | Interest: Programming; subjects: Computer Science, Mathematics; preference: Building apps; skills: Python, Java, Git, SQL, Data Structures | Software Developer (85 %) |
| Data enthusiast | Interest: Data analysis; subjects: Mathematics, Statistics; preference: Working with data; skills: Excel, SQL, Statistics, Power BI, Critical Thinking | Data Analyst (85 %) |
| Creative designer (High School) | Interest: Design & creativity; subject: Art & Design; preference: Creative & visual work; skills: Figma, Typography, Creativity, Communication | UI UX Designer (70 %) |

Changing the inputs changes the ranking, the explanation and the roadmap.

## Run the tests

```powershell
python -m unittest tests.test_expert_system -v
```

These checks need only Python, pandas, numpy and pdfplumber (not Streamlit). They verify the rule base, that different profiles give different and sensible rankings, and the resume skill extraction.

## Current prototype limitations

- Only **14 careers** are supported by the rules and roadmaps (the other careers in `data/careers.json` are not used yet).
- Rules and weights are hand-written examples, not learned from real student data, and are not professional career advice.
- Skill matching is by exact skill name (ignoring case); synonyms are not understood.
- Resume parsing works only for text PDFs (not scanned images) and uses keyword matching.
- Roadmaps are generic per career; only the course suggestions depend on your skill gaps.
- No login, database, chatbot or deployment.

**Future scope:** extend the rules to all 188 careers, learn rule weights from data, an AI mentor chatbot, downloadable PDF career report, saved profiles.

---

## Author

**Abhishika** — NIT Hamirpur  
GitHub: [ika12345](https://github.com/ika12345)

## License

This project includes MIT-licensed reused components. See [LICENSE](LICENSE)
for the complete license notice.
