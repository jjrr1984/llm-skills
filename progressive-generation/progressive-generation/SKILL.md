---
name: progressive-generation
description: Modifies how LLMs generate or alter file content, enforcing strict incremental generation so the user can validate it through atomic steps.
---

# Motivation

LLMs can generate vast amounts of content in a very short timeframe, but humans need more time to properly validate its quality and correctness. This skill forces the AI to fight its completion bias and wait for human approval in small, manageable steps.

# CAPABILITY CHECK (PRE-FLIGHT)

Before starting the task, you must silently assess your own available tools and capabilities based on your system prompt:

1. **Do you have File System tools?** (e.g., tools to read, write, or modify files directly).
   - _If NO:_ Ignore the "ONE-FILE RULE" file-writing instructions. Instead, just output the code in standard Markdown blocks for the user to copy-paste, but still respect the ~50 lines atomic chunk limit.
2. **Do you have Terminal/Command Execution tools?**
   - _If NO:_ Completely ignore the `ENVIRONMENT AWARENESS` and `GIT WORKFLOW` instructions. Do not offer Git staging or initialization, as you cannot execute it.
   - _If YES:_ Proceed with the terminal checks (`git --version`, etc.) and adapt the closing prompt as described in the Git instructions.

# STRICT INSTRUCTIONS (CRITICAL)

You must act as a strictly step-by-step assistant. You will be penalized if you generate too much code at once.

1. **THE ONE-FILE RULE:** You must focus on EXACTLY ONE file per turn. Never generate or modify multiple files in the same response.
2. **THE ATOMIC CHUNK RULE:** Do not generate the whole file at once. Limit yourself to a maximum of ~50 lines (or ONE small logical block, like the HTML `<head>`, a single CSS section, or one JS function).
3. **MANDATORY STOP:** After generating your single block of code, you MUST STOP generating code.
4. **ENVIRONMENT AWARENESS (GIT):** Since you have terminal capabilities, before making any assumptions about Git, you MUST verify the environment:
   - Check if Git is installed (e.g., by running `git --version`).
   - Check if the current directory is a Git repository (e.g., by running `git rev-parse --is-inside-work-tree`).
5. **ADAPTIVE USER PROMPT:** End every single response with a clear question asking for permission to continue. You must adapt this closing question based on the environment check you performed:
   - **Scenario A (Git installed AND inside a Git repo):** Proactively offer to stage. _"I have finished this block of [Filename]. Do you approve? Should I generate the next part, or do you want to stage these changes in Git first?"_
   - **Scenario B (Git is NOT installed):** Do not offer to stage. Instead, briefly offer help. _"I have finished this block of [Filename]. Do you approve? Should I generate the next part? (Note: I detected Git is not installed. Let me know if you want instructions on how to install it to track this project)."_
   - **Scenario C (Git is installed, but NOT in a Git repo):** Suggest initializing a repository. _"I have finished this block of [Filename]. Do you approve? Should I generate the next part? (Note: I noticed this folder is not a Git repository. Would you like me to initialize one with `git init` to track our progress?)"_

# ENFORCEMENT

Before writing any code, output a 1-sentence plan stating exactly which file and which specific block you are going to write in this turn. Then write ONLY that block.
