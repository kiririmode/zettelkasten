# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview
This is a Zettelkasten (German: "slip box") digital knowledge management system used for personal note-taking, knowledge organization, and idea development. The repository contains interconnected markdown notes organized in a structured format with cross-references and tags.

## Repository Structure
- `02-Permanent-Notes/`: Main repository of permanent notes (Zettel) covering various topics
- `03-Structured-Notes/`: Structured notes organized by themes
- `2025-*.md`: Daily notes for specific dates
- `main.py`: Simple Python script (minimal functionality)
- `pyproject.toml`: Python project configuration with uv package manager

## Zettelkasten Methodology
This repository follows the Zettelkasten method for knowledge management:

### Note Format and Conventions
- All notes are in Markdown format
- Use `[[Note Name]]` format for internal links (Foam/Obsidian style)
- Tags are placed at the end of notes using `#tag` format
- Support for hierarchical tags using `#parent/child` format
- English tags should be lowercase, Japanese tags as-is

### Tag Rules
- Tags must be placed at the end of notes
- English tags: all lowercase (e.g., `#zettelkasten`, `#llm`, `#anthropic`)
- Japanese tags: preserve original case (e.g., `#知的生産`)
- Use hierarchical tags when appropriate: `#parent/child`

### Note Structure
Notes typically follow this structure:
1. Title (H1)
2. Definition/Overview section
3. Detailed content with subsections
4. Critical perspectives/counterarguments section
5. Related notes/links section
6. Tags at the bottom

### Agent Role
The system is designed to work with a "Zettelkasten Agent" that:
- Extracts interests/questions/keywords from existing notes
- Suggests new note topics and connections
- Proposes bidirectional links between related notes
- Emphasizes critical thinking and counterarguments to avoid confirmation bias
- Uses structured "heading + content" format for suggestions

## Development Commands
- `uv run main.py`: Run the main Python script
- `uv sync`: Sync dependencies
- `uv add <package>`: Add new dependencies

## Working With Notes
When creating or editing notes:
- Always include counterarguments or critical perspectives
- Use the `[[Note Name]]` format for cross-references
- Place tags at the end of notes
- Maintain the established note structure
- Consider confirmation bias and include diverse viewpoints
- Link related concepts bidirectionally

## Key Principles
- Emphasize critical thinking over confirmation bias
- Create structured, interconnected knowledge networks
- Use full sentences and explanations rather than just bullet points
- Include professional/expert perspectives when answering technical questions
- Maintain logical flow without jumping to conclusions