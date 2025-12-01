# Release Notes Generation Prompt

**Use Case**: Generate customer-facing release notes from GitHub repository

## When to Use

- Release documentation
- Customer communication
- Changelog generation

## Prompt

# Generate customer-facing release notes for a software release based on the information from GitHub repository.

Please use non-technical language. 

# Requirements:

## Document Structure
- Title with release name/version
- Release date
- Use emojis and clear headers
- End with support info

## Language
- Non-technical, customer-focused
- Explain what users can do with new features
- Professional but friendly tone
- Clear and easy to scan

## Content Organization
- Start with major features
- Group related features 
- Include detailed PR-by-PR section
- Highlight UI changes

## Required Sections
1. "What's New and Improved" - major features
2. "User Interface Changes" - UI improvements
3. "Detailed Release Notes by Feature" - PR breakdown with:
   - What Changed (non-technical)
   - User Impact (benefits)
   - UI Changes (interface improvements)
4. "Summary of UI Changes" - categorized list

## UI Changes Documentation
- New UI Elements
- Improved UI Elements
- Fixed UI Issues
- Visual Enhancements

## Content Requirements
- Include PR numbers and feature names
- Focus on customer features, not infrastructure
- Use bullet points
- Explain the "why" behind changes

## Tone and Style
- Use "We improved..." or "We added..."
- Focus on benefits: "You can now..."
- Avoid technical details
- Use positive language

## Formatting
- Clear headers with emojis
- Bullet points for lists
- Bold text for key features
- Consistent formatting

# Goal: 
Help customers understand what's new, how it benefits them, and what interface changes they'll notice, all in clear, non-technical language.

Generate a document and the name of the document should be 'ReleaseNotes_<Version_ID>_<YEAR>.md'.
