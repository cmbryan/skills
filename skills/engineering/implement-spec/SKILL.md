---
name: implement-spec
description: "Implement the result of /to-spec and /to-tickets in code."
disable-model-invocation: true
---

You have been provided a spec. This spec should have tickets associated with it, describing how to implement the spec.

The issue tracker should have been provided to you. If not, tell the user to run `/setup-matt-pocock-skills`.

The goal is the entire spec implemented on a single **integration branch**, with every ticket resolved the way the issue tracker closes work.

The tickets are not a list of steps. They are a **task graph** with blocking relationships between them. This means there is always a **frontier** of tickets which are ready to be grabbed.

Tickets come in two kinds, told apart by the labels in the repo's triage-labels doc:

- **Agent tickets** (`ready-for-agent`) are yours to implement.
- **Human tickets** (`ready-for-human`) are manual steps only the user can do. Never implement or close one, and never start a subagent for one.

The frontier is the open agent tickets whose blockers are all done. A blocker is done once its work has merged into the integration branch (agent tickets) or the user has closed it (human tickets). Track merges yourself: the tracker's blocked-by state only changes when a ticket closes, which for agent tickets is the end of the run. Re-read the tracker whenever you recompute the frontier, because the user may have closed human tickets meanwhile.

Communication to and from subagents should be sparse. Communicate primarily through **context pointers**: to the spec, tickets, research notes, and previous commits. Don't duplicate information already available via pointers.

**Implementer subagents** should be run in the background where possible for maximum concurrency.

## Steps

1. Read the spec and tickets to understand the task graph.

2. (optional) Use an **exploration subagent** to conduct any exploration required by the tickets - relevant codebase files or external documentation. Ensure the exploration subagent can save files - it should save its markdown notes in a directory outside the repo, accessible by all future subagents. This lets **implementer subagents** focus on implementation rather than exploration.

3. Create the integration branch. If the issue tracker closes work through PRs, or the user asks for one, open a draft PR after the first merge in step 5 (a branch with no commits ahead of main can't open one). Mark it as closing the agent tickets only. Reference the spec and any human tickets without closing them.

4. Use **implementer subagents** to implement each ticket, each in its own worktree on its own branch. Each implementer subagent:
   - confirms its worktree is based on the integration branch before starting, and resets onto it if not;
   - calls the Skill tool with `tdd` to build the ticket;
   - merges the integration branch tip into its own branch before reporting done

5. Once an **implementer subagent** completes, merge its work to the integration branch with a **merger subagent**.

6. If this changes the **frontier** of available tickets, kick off more **implementer subagents** to work on the new tickets. This allows for maximum concurrency.

   Also check the human tickets. When one has all its blockers done, tell the user it is now theirs, with a link and one line on why. Keep working the frontier meanwhile. If the human ticket needs the code on the main branch and it is still only on the integration branch, say so, mark the PR ready, and ask the user to merge it first.

7. **Pause when blocked on the user.** If the frontier is empty but agent tickets remain that are blocked by open human tickets, stop. Report which human tickets are blocking, which agent tickets wait on each, and what the user has to do, then end your turn. When the user says to continue, go back to step 6 with fresh tracker state.

8. Once all agent tickets are complete, call the Skill tool with `code-review` on the integration branch. Fix all issues raised by the code review in a single **implementer subagent**.

9. If a draft PR exists, mark it ready for review. Otherwise, resolve each agent ticket the way the issue tracker closes work, and report the integration branch. List any human tickets still open, and do not close the spec while any remain.

10. Clean up all **implementer subagent** worktrees.
