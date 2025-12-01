# AI Quality Intelligence SDLC - Copilot Instructions

This project contains AI prompts and results for injecting Quality Intelligence across the Software Development Lifecycle (SDLC).

## Project Structure

- `01-Requirements/` - Prompts and results for requirements analysis
- `02-Development/` - Prompts and results for development phase QA
- `03-Testing/` - Prompts and results for test planning
- `04-Release/` - Prompts and results for release quality assessment
- `05-Post-Release/` - Prompts and results for root cause analysis

## Guidelines

- Store prompts as markdown files in the `prompts/` subfolder of each phase
- Store AI-generated results in the `results/` subfolder of each phase
- Use descriptive filenames that indicate the purpose
- Include date stamps in result filenames where appropriate

## Naming Conventions

- Prompts: `<purpose>-prompt.md`
- Results: `<PR_ID>_<purpose>_<DDMMYYYY>.md`
- Release notes: `ReleaseNotes_<Version_ID>_<YEAR>.md`
- Risk assessments: `Release_<Version_ID>_<YEAR>_Risk_Assessment.md`
