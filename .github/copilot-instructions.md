# AI Quality Intelligence SDLC - Copilot Instructions

This project contains AI prompts and real-world examples for applying Quality Intelligence across the Software Development Lifecycle (SDLC). Each phase includes reusable prompt templates and sample outputs.

## Project Structure

- `01-Requirements/` — Requirements analysis prompts and results (PO gap analysis, QA testing strategy)
- `02-Development/` — Development-phase quality prompts and results (branch quality assessment)
- `03-Testing/` — Test planning prompts and results (acceptance test cases)
- `04-Release/` — Release quality assessment prompts and results (risk assessment, release notes)
- `05-Production/` — Post-release prompts and results (root cause analysis, E2E test recommendations)

## Guidelines

- Store prompt templates as Markdown files in the `prompts/` subfolder of each phase.
- Store AI-generated results in the `results/` subfolder of each phase.
- Use descriptive filenames that indicate the purpose and context.
- Include date stamps in result filenames where appropriate.

## Naming Conventions

- Prompts: `<purpose>-prompt.md`
- Results: `<PR_ID>_<purpose>_<DDMMYYYY>.md`
- Release notes: `ReleaseNotes_<Version_ID>_<YEAR>.md`
- Risk assessments: `Release_<Version_ID>_<YEAR>_Risk_Assessment_Analysis.md`
- Root cause analyses: `<JIRA_KEY>_<DDMMYY-HHMM>_Root_Cause_Analysis.md`
