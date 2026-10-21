# TalentBook

A demo of a recruitment tool for Canadian agencies, engineering firms and in-house recruiting teams. Recruiters search the candidates they have worked with in plain English. Each candidate profile pulls together memory cards, contact history, skills, pain points, location, expected pay and work preferences, and ranks the candidate against open jobs.

One TalentBook system serves one company (single tenant). The demo company is Northbound Engineering Recruitment, an engineering recruitment firm with recruiters in Toronto, Montréal and Calgary. Data from different companies is never mixed; inside the company, each recruiter manages their own desk.

This is a front-end demo with no backend. Open `index.html` in a browser. Data is seeded in the page and saved in the browser's localStorage.

## Demo accounts

All three work for Northbound and use the password `demo`. Each recruiter sees only their own candidates, clients and jobs.

| Recruiter | Desk | Email |
|---|---|---|
| Maya Chen | Structural, Civil & Construction, Toronto | maya.chen@northboundeng.example |
| Julien Tremblay | Electrical & Automation, Montréal | julien.tremblay@northboundeng.example |
| Priya Natarajan | Energy & Geotechnical, Calgary | priya.natarajan@northboundeng.example |

## What the demo does

- **Ask**: natural-language search such as "P.Eng structural engineers in the GTA under 130k" or "candidates not contacted in 3 months". The page shows how it read the query as removable chips. In the demo a rule-based parser stands in for the LLM.
- **Home**: counts for the desk, a follow-up queue (60+ days without contact) and open jobs with their best match.
- **Candidate profile**: AI-style summary, memory cards, pain points and reasons to move, timeline of calls and emails, key facts (pay in CAD, work mode, relocation, notice, work authorization, languages), skills with years, matching open jobs, and consent status.
- **Résumé upload**: PDF, Word (.docx) or text, read in the browser (pdf.js and mammoth.js from cdnjs). Fields are extracted for review before saving. It warns when the person is already on a colleague's desk, and it does not store protected-ground details.
- **Jobs**: candidates ranked out of 100 (skills 55, location 20, salary 15, credentials 10) with gaps listed, a pipeline, the client's preferences, and a job-posting check against the rules of the job's province.
- **Clients**: hiring preferences, things to avoid, contacts, fee and guarantee terms.
- **Compliance**: consent and retention issues, CASL status, and job postings to fix.

## Canadian rules the design follows

This is a summary for product design, not legal advice.

| Area | Rule | How TalentBook handles it |
|---|---|---|
| Privacy | PIPEDA federally; Québec Law 25 (fully in force since Sept 2024); Alberta and BC PIPA | Consent method, date and retention date on every candidate. Access-request export. Use limited to recruiting. |
| Anti-spam | CASL: email/SMS job alerts need express consent, or implied consent that lapses after 2 years | CASL status per candidate, with a warning when logging contact on implied consent |
| Ontario job postings | ESA rules from 1 Jan 2026 for employers with 25+ staff: pay range no wider than $50k (for jobs up to $200k), disclose AI screening, no "Canadian experience" requirement, say whether the vacancy exists, reply to interviewed candidates within 45 days, keep records 3 years | Automatic posting check per job |
| Pay transparency elsewhere | BC (since Nov 2023) and PEI require pay ranges in postings; Newfoundland and Labrador's rule is not yet in force | Province-specific check |
| Recruiter licensing | Ontario recruiters and temp help agencies need a licence since 1 July 2024 | The company's licence number, shown in the posting check |
| Québec language | Charter of the French Language (Bill 96): postings and work documents in French | Warns when a Québec job has no French version |
| Human rights | No screening on protected grounds | Résumé parsing ignores DOB, marital status and similar; client-preference form reminds recruiters |
| Professional titles | "Engineer" is a protected title: PEO (ON), OIQ (QC), APEGA (AB), EGBC (BC) | Designations tracked separately from job titles and matched against job requirements |
| Work authorization | Citizen, PR, open or employer-specific permit (LMIA) | Stored per candidate; LMIA need lowers the match score and can be filtered out |

Sources: [Ontario licensing for recruiters](https://www.ontario.ca/page/licensing-temporary-help-agencies-and-recruiters), [Ontario 2026 posting rules (HCAMag)](https://www.hcamag.com/ca/specialization/employment-law/ontarios-new-pay-transparency-and-ai-disclosure-laws-what-employers-need-to-know-now/535727), [Ontario rules (Achkar Law)](https://achkarlaw.com/insights/ontarios-new-pay-transparency-rules/), [Pay transparency by province](https://vhr.sites.sourceflow.co.uk/blog/canada-pay-transparency-laws-2026), [Québec candidate privacy guide (HR Reporter)](https://www.hrreporter.com/focus-areas/automation-ai/job-candidates-and-privacy-new-guide-helps-quebec-employers-navigate-laws/392243).

## Before going live

1. Backend and database with real authentication (the company's SSO), row-level access per recruiter and a company admin role. Each company gets its own deployment and database.
2. Canadian data residency (for example AWS ca-central-1) and a privacy impact assessment, required under Law 25 for Québec data, including before sending data outside Québec.
3. Real LLM search and résumé parsing, with the AI-use disclosure Ontario now requires and a human review step.
4. Retention jobs that delete or anonymise candidates when their retention date passes.
5. French interface for Québec clients.
6. Email and calendar sync so contact history fills in automatically, with CASL unsubscribe handling.
