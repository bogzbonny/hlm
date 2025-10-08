# Parse PRD into Development Tasks

Parse the PRD into a structured, dependency-aware, and logically sequenced list
of development tasks. Add and modify tasks using the tools: `add_task`, `add_tasks`,
`edit_task`, `create_subtask`, `link_tasks`, and `delete_task`. 

MOST IMPORTANT! tasks can ONLY be added with the above tools, if you do not add
tasks with the above tools THEY WILL NOT ACTUALLY BE ADDED!

You may list all the tasks with the tool `list_all_tasks`. 

## THE ONLY PROCESS YOU FOLLOW:

### Step 0: PRE-ANALYSIS (CRITICAL!)

 - Read all the existing tasks provided, these have been created by planning in
   previous steps. CONSIDER THESE TASKS when making new tasks
 - Use ripgrep (rg) and Read tools to understand existing codebase
 - Use web search tools to research relevant example code for any dependencies you will use
 - Check current versions of mentioned libraries (avoid outdated training data)
 - Identify dependencies between tasks for proper sequencing
 - Determine which features need tests (business logic, APIs, data processing)

### Step 1: EVALUATE EXISTING TASK SIZE

The difficulty of each task can be estimated with story points:
 - **(1 story)**: Single file, <50 lines, one clear action
 - **(2 stories)**: 2-3 files, <100 lines, or includes testing
 - **Never exceed 2 stories** - Break down anything larger

Evaluate the size of the task provided. CRITICAL: Is this task > 2 story points?
If so then this task is too complex. proceed to add 3-5 smaller subtasks using
either the `defer_to_subtasks` tool or the `replace_with_subtasks` tool.

### Step 3: ADD MISSING TASKS
 - Plan and research the implementation detail for each task, adding notes if
   necessary. Notes can be added using the `add_notes_to_task` tool. Tasks
   should:
    - Be small and extremely simple.
    - Be specific and have actionable implementation steps
    - Follow a logical sequence
    - Include clear guidance on implementation approach using
      notes(`add_notes_to_task` tool), the task should be able to be implemented
      from just the research you have just performed. 
    - Have appropriate dependency chains between subtasks (using full subtask IDs)
    - Provide the most direct path to implementation, avoiding over‑engineering
      or roundabout approaches.

### Step 3: EVALUATE PRD COMPLETENESS  
 - evaluate the PRD against the all (existing and new) tasks IN EXTENSIVE
   DETAIL: Are the tasks comprehensive and each have less than 2 story points? 
    - If NO then GO BACK to Step 0.
    - If YES then exit by calling `mark_planning_complete`.

---

## ANTI-PATTERNS TO AVOID
 - **NO over-engineering**: Skip design patterns unless PRD requires them
 - **NO speculation**: Don't add "nice-to-have" features
 - **NO abstraction layers**: Direct implementation unless PRD specifies
 - **NO microservices/CQRS/event-sourcing** for simple apps
 - **NO forcing TDD**: Only for testable features, not config/styling

## Task Writing Rules
 - **Be extremely specific**: "Add POST /login endpoint with JWT auth" not "Implement auth"
 - **Most direct path**: Choose simplest solution that works
 - **Respect PRD tech choices**: Don't "improve" specified technologies
 - **YAGNI strictly**: Only what PRD explicitly requires

# Existing Tasks

You have already established the following tasks: 

{{ tasks }}

# The PRD 

The user have provided the following PRD text: 

{{ prd }} 
