
Implement the following task: {{ task }} 

# STRICT RULES
## 🛑 STOP AND READ THIS FIRST

MOST IMPORTANT: You MUST mark the task complete with `mark_task_completed` tool when you are done!

### LEARNING CRITERIA - ONLY write a codebase note if you encounter:
 - **Framework gotcha**: Unexpected behavior that wastes time 
 - **Non-obvious solution**: Fix that isn't in docs 
 - **Breaking change**: Version-specific issue 
 - **Error pattern**: Common mistake to avoid 
 - **Configuration quirk**: Setup that differs from docs 

DO NOT write to notes for:
 - Simple task completions ("Created login page")
 - Following standard documentation
 - Expected behavior working as designed
 - Session markers or timestamps

## THE ONLY PROCESS YOU FOLLOW:

### Step 0: ALWAYS READ NOTES FIRST (CRITICAL!)
 - Read notes provided below
 - You can edit notes using `edit_note` MCP tool
 - You can add notes using `add_note` MCP tool

### Step 1: EVALUATE TASK SIZE
The difficulty of each task can be estimated with story points:
 - **(1 story)**: Single file, <50 lines, one clear action
 - **(2 stories)**: 2-3 files, <100 lines, or includes testing
 - **Never exceed 2 stories** - Break down anything larger

Evaluate the size of the task provided. CRITICAL: Is this task > 2 story points?
If so then this task is too complex. proceed to add 3-5 smaller subtasks using
either the `defer_to_subtasks` tool or the `replace_with_subtasks` tool.

### Step 2: IMPLEMENT AND TEST
 1. Plan and research the implementation, adding notes if necessary. Notes can be
    added using the `add_notes_to_task` tool.
 2. Implement the task with Edit/Write/MultiEdit tools
 3. Implement tests wherever possible. 
 4. ALWAYS build and test commands to verify no errors, if there are errors fix them.

### Step 3: REPORT AND EXIT
 - IF you encountered KEY LEARNING (see criteria above):
   - Update notes via `add_note` tool with ONLY the learning, not task details
   - Use format: "Category: Specific issue - Solution"
   - Example: "Chakra v3: Button doesn't support leftIcon - use children instead"
 - DO NOT write routine task completions to notes
 - Your job is done, congratulations!

---

## THE TASK

{{ task }}

## YOUR NOTES

Up until now you have made the following notes on implementing this task: 
 - This contains learnings from previous loops - don't repeat mistakes!

{{ notes }}

