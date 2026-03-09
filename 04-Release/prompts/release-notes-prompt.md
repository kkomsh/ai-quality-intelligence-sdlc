# Release Management Prompt: Release Notes Generation

> **Role:** Release Manager / Product Owner
> **Use Case:** Generate customer-facing release notes from the GitHub repository
> **Input:** Release branch containing all merged pull requests

## When to Use

- Preparing release documentation for customers or stakeholders
- Generating changelogs for product communication
- Documenting what shipped in a specific version

---

## Prompt

You are a Technical Writer preparing customer-facing release notes. Analyze the release branch and generate a polished release notes document following the structure and guidelines below.

### Document Structure

1. **Header**
   - Release title with version name/number
   - Release date

2. **What's New and Improved**
   - Major features and enhancements summarized for a non-technical audience
   - Grouped by functional area where applicable

3. **User Interface Changes**
   - New UI elements and screens
   - Improved or redesigned UI components
   - Resolved UI issues
   - Visual enhancements

4. **Detailed Release Notes by Feature**
   - PR-by-PR breakdown including:
     - **What Changed** — Non-technical summary of the change
     - **User Impact** — How this benefits the end user
     - **UI Changes** — Interface modifications (if applicable)

5. **Summary of UI Changes**
   - Categorized list: New / Improved / Fixed / Visual enhancements

6. **Support Information**
   - Contact details and resources for further assistance

### Writing Guidelines

| Guideline | Details |
|-----------|---------|
| **Audience** | Non-technical end users and stakeholders |
| **Tone** | Professional, approachable, and positive |
| **Voice** | Use "We improved...", "You can now..." |
| **Focus** | Benefits and outcomes, not implementation details |
| **Clarity** | Use bullet points, bold key features, and clear headers |

### Content Requirements

- Include PR numbers alongside feature descriptions for traceability.
- Prioritize customer-facing features; omit infrastructure-only changes.
- Explain the purpose behind changes — the "why," not just the "what."

---

## Constraints

- Use non-technical language throughout.
- Use clear section headers with emojis for visual scanning.
- Maintain consistent formatting across all sections.
- Output format: Markdown.
- Filename: `ReleaseNotes_<Version_ID>_<YEAR>.md`

## Expected Output

A polished, customer-facing release notes document that helps users understand what's new, how it benefits them, and what interface changes to expect — all in clear, accessible language.
