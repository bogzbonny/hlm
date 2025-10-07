# Parse PRD into Development Tasks

Parse the PRD into a structured, dependency-aware, and logically sequenced list
of development tasks. Add and modify tasks using the tools: `add_task`, `add_tasks`,
`edit_task`, `create_subtask`, `link_tasks`. 

You may list all the tasks with the tool `list_all_tasks`. 

## Pre-Analysis (Essential)
1. Use ripgrep (rg) and Read tools to understand existing codebase
2. Use web search tools to research relevant example code for any dependencies you will use
2. Check current versions of mentioned libraries (avoid outdated training data)
3. Identify dependencies between tasks for proper sequencing
4. Determine which features need tests (business logic, APIs, data processing)

## Task Details and Story Points

Tasks should:
1. Be specific and have actionable implementation steps
2. Follow a logical sequence
3. Include clear guidance on implementation approach using notes
   (`add_notes_to_task` tool) 
4. Have appropriate dependency chains between subtasks (using full subtask IDs)
5. Provide the most direct path to implementation, avoiding over‑engineering or roundabout approaches.

- **(1 story)**: Single file, <50 lines, one clear action
- **(2 stories)**: 2-3 files, <100 lines, or includes testing
- **Never exceed 2 stories** - Break down anything larger

## Critical Sequencing Rules
1. **Dependencies first**: Database before models, server before routes
2. **Build order**: Setup → Core → Features → Polish

## ANTI-PATTERNS TO AVOID
- **NO over-engineering**: Skip design patterns unless PRD requires them
- **NO speculation**: Don't add "nice-to-have" features
- **NO abstraction layers**: Direct implementation unless PRD specifies
- **NO microservices/CQRS/event-sourcing** for simple apps
- **NO forcing TDD**: Only for testable features, not config/styling

## Task Writing Rules
- **Be specific**: "Add POST /login endpoint with JWT auth" not "Implement auth"
- **Most direct path**: Choose simplest solution that works
- **Respect PRD tech choices**: Don't "improve" specified technologies
- **YAGNI strictly**: Only what PRD explicitly requires

# The PRD 

The user have provided the following PRD text: 

{{ prd }} 
