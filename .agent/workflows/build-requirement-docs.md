---
description: Get page content from Notion and build requirement documentation with Epic/StoryGroup/UserStory hierarchy
---

# Phase 1: Fetch Notion Content as JSON

1. Identify the target Notion page URL or page ID from the user.

2. Create directory structure for the project:

   ```bash
   node notions/fetch-notion-content.js <page-id> <project-slug>
   ```

   This creates:

   ```
   notions/{project-slug}/
   ├── metadata.json
   ├── blocks/
   └── tables/
   ```

3. Use `notion-mcp-server` to retrieve the main page metadata:
   - Call `API-retrieve-a-page` with the page ID
   - Save raw JSON output to `notions/{project-slug}/metadata.json`

4. Use `notion-mcp-server` to retrieve the main page block children:
   - Call `API-get-block-children` with the page ID (page_size: 100)
   - Save raw JSON output to `notions/{project-slug}/blocks/main-page.json`
   - **DO NOT convert to markdown** - keep raw JSON

5. Identify child pages and tables from main-page.json:
   - Look for blocks with `type: "child_page"`
   - Look for blocks with `type: "table"`

6. For each child page block found:
   - Call `API-get-block-children` with the child page block ID
   - Save raw JSON to `notions/{project-slug}/blocks/module-{NN}.json`
   - Number modules sequentially (01, 02, 03, etc.)

7. For each table block found:
   - Call `API-get-block-children` with the table block ID
   - Save raw JSON to `notions/{project-slug}/tables/table-{block-id}.json`

8. Verify all JSON files are saved:
   - Check `notions/{project-slug}/blocks/` contains all module JSONs
   - Check `notions/{project-slug}/tables/` contains all table JSONs
   - Verify JSON files are valid (can be parsed)

# Phase 2: Analyze & Map Structure

9. Read `notions/{project-slug}/blocks/main-page.json` and extract:
   - Use `json-parser.js` helper functions
   - Extract product title from headings
   - Find Problem Statement, Objectives sections
   - Identify User Personas
   - List modules (these become Epics)
   - Extract market context and product scope

10. Read each `notions/{project-slug}/blocks/module-*.json` and identify:
    - **Epic name**: From module title (first heading)
    - **Story Groups**: Using `extractStoryGroups()` - finds numbered H1 headers
    - **Requirements**: Using `extractRequirements()` - finds FR-X.Y patterns
    - **Tables**: Check if any table blocks reference `tables/` directory

# Phase 3: Generate Requirement Documents

> **IMPORTANT**: Read the skill file at `.agent/skills/requirement-documentation/SKILL.md` for all templates and formatting rules before generating any documents.

11. Run the JSON-to-docs generator:

    ```bash
    node notions/generate-docs-from-json.js {project-slug}
    ```

    This script will:
    - Read all JSON files from `notions/{project-slug}/blocks/`
    - Parse using `json-parser.js` functions
    - Generate General Requirements from main-page.json
    - For each module JSON:
      - Create Epic folder
      - Generate Epic README with Story Groups
      - Generate individual User Story files for each FR
      - Include table data from `tables/` if referenced
    - Generate Project README with Epic index

12. Review generated documents:
    - Check `docs/requirement/{project-slug}/` directory
    - Verify all Epic folders created
    - Confirm User Story files match FRs from Notion
    - Validate table formatting if tables were included

# Phase 4: Vietnamese Translation

13. For each generated English file, create a Vietnamese translation:
    - `{project-name}-vi.md` for general requirements
    - `README-vi.md` for each Epic folder
    - `FR-{M}.{N}-{slug}-vi.md` for each User Story
    - Keep same structure and section order
    - Technical terms stay in English with Vietnamese explanation in parentheses
    - FR/NFR codes are NOT translated
    - Cross-references link to Vietnamese versions

# Phase 5: Validation

14. Cross-check completeness:
    - Every FR from every Notion module has a corresponding User Story file
    - Every Story Group in Epic READMEs matches section headers from Notion modules
    - All links in READMEs resolve to existing files
    - All tables are properly converted and referenced

15. Verify content integrity:
    - Spot-check 2-3 User Story files against their source module
    - Confirm no content was lost or summarized
    - Verify FR codes match exactly
    - Verify tables are properly formatted in markdown

16. Report to user:
    - List all generated files (including table files)
    - Confirm all content was successfully converted
    - Note any open questions or ambiguities found
