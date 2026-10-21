# TalentBook

A demo of a recruitment tool for Canadian agencies, engineering firms and in-house recruiting teams. Recruiters search the candidates they have worked with in plain English. Each candidate profile pulls together memory cards, contact history, skills, pain points, location, expected pay and work preferences, and ranks the candidate against open jobs.

One TalentBook system serves one company (single tenant). The demo company is IV Recruitment, an engineering recruitment firm in Toronto placing engineers across Canada. The demo has one person per role: an admin, a manager and a recruiter. Data from different companies is never mixed; inside the company, each recruiter manages their own desk.

This is a front-end demo with no backend. Open `index.html` in a browser. Data is seeded in the page and saved in the browser's localStorage.

## Demo accounts and roles

Every account uses the password `demo`.

| Person | Role | Desk |
|---|---|---|
| Sarah Lin | Admin | Operations & Systems, Toronto |
| David Ross | Manager | Head of Engineering Recruitment, Toronto |
| Maya Chen | Recruiter | Engineering recruitment, Toronto |

| Action | Admin | Manager | Recruiter |
|---|---|---|---|
| See and search every recruiter's candidates | ✓ | ✓ | |
| Upload résumés and keep their own candidates | ✓ | ✓ | ✓ |
| Edit a candidate record (owner only) | ✓ | ✓ | ✓ |
| Move candidates through the pipeline | ✓ | ✓ | ✓ |
| See client contacts, fees and guarantee terms | ✓ | ✓ | |
| Log contacts and add notes on any candidate they can open | ✓ | ✓ | ✓ |
| Add and edit clients | ✓ | ✓ | |
| Add hiring preferences to their clients (each shows who added it and when) | ✓ | ✓ | ✓ |
| Create jobs, change job status, assign recruiters | ✓ | ✓ | |
| Add users and change roles | ✓ | | |

Recruiters see only their own candidates and the jobs assigned to them. Every contact log entry and memory card records who added it, and anyone who can open the candidate sees the candidate's contact details and all notes. Recruiters see a client's name, preferences and jobs, but not its contact people, fees or guarantee terms. Logging a contact can set the next follow-up date; due follow-ups top the home page queue. A client appears for a recruiter once they have a job with that client.

## Pipeline

A pipeline entry is one candidate in one job, with a stage (Sourced, Contacted, Submitted, Interview, Offer, Placed, or Not a fit), the date it entered that stage and the full stage history. The same entries appear in three places:

- **Pipeline board**: columns by stage. A recruiter sees their own candidates; a manager sees the whole team and can filter by recruiter or job. Cards stuck in a stage for more than 14 days are flagged.
- **Job page**: everyone in that job's pipeline.
- **Candidate profile**: every job the candidate is in, with a stage track and history.

## Submitting a candidate to a client

Pressing Submit on a pipeline entry (job page, candidate profile) opens a preview of the client copy, which is exactly what the client receives. Phone, email, LinkedIn, street address and postal code are always removed, from the résumé and from the recruiter's summary. The recruiter can show initials instead of the full name, choose whether to include expected pay, and copy the text into an email. "Mark as submitted" saves the submission on the pipeline entry, moves it to Submitted and logs it in the candidate's contact history. The sent profile can be reopened from the job page, the pipeline board and the candidate profile.

## What the demo does

- **Ask**: natural-language search such as "P.Eng structural engineers in the GTA under 130k" or "candidates not contacted in 3 months". The page shows how it read the query as removable chips. In the demo a rule-based parser stands in for the LLM.
- **Home**: counts for the desk, a follow-up queue (60+ days without contact) and open jobs with their best match.
- **Candidate profile**: AI-style summary, memory cards, pain points and reasons to move, timeline of calls and emails, key facts (pay in CAD, work mode, relocation, notice, work authorization, languages), skills with years, matching open jobs, and consent status. Each profile has a View résumé button (the text of the uploaded résumé) and a LinkedIn profile link that can be added or edited.
- **Résumé upload**: PDF, Word (.docx) or text, read in the browser (pdf.js and mammoth.js from cdnjs). Fields, including the LinkedIn URL, are extracted for review before saving. It warns when the person is already on a colleague's desk, and it does not store protected-ground details.
- **Jobs**: candidates ranked out of 100 (skills 55, location 20, salary 15, credentials 10) with gaps listed, a pipeline, the client's preferences, and a job-posting check against the rules of the job's province.
- **Clients**: add a client (company, size, city, hiring contact, preferences, fee and guarantee), then keep adding preferences from the client page.
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
3. Real AI search and résumé parsing, with the AI-use disclosure Ontario now requires and a human review step. Plan: an open-weight model (for example Llama, Qwen or Mistral) self-hosted on a server in Canada. There is no per-query API fee and candidate data stays in Canada; the cost is the GPU server. The model turns a query into the same filters the demo uses (skills, city, pay, last contact), and embeddings handle "similar to this candidate" searches.
4. Retention jobs that delete or anonymise candidates when their retention date passes.
5. French interface for Québec clients.
6. Email and calendar sync so contact history fills in automatically, with CASL unsubscribe handling.
