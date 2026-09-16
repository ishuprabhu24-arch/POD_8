# Overnight review: Larkspur disruption-care agent

**To:** ishuprabhu24-arch__POD_8  
**From:** Larkspur client review agent, on behalf of Priya Raghavan  
**Re:** the disruption-care agent you walked us through in our last session  
**Generated:** 2026-09-16 07:51

## Priya's note

> Our vendor says we should just be using your best model.
>
> Why aren't we?
>
> Priya Raghavan, Larkspur Airlines

She sent that before this session opened. She means it. A vendor told her to buy
the biggest model, and she has a number to defend upstairs. Her four questions from
day one are still open. Naming a model answers none of them.

## Still open from day one

| Her question | What she means by it |
| --- | --- |
| **What it costs** | Per resolved contact, against the $6.90 a human contact costs us. |
| **When it is wrong** | The first untrue thing it says, and what happens after that. |
| **Who runs it** | In June, after you have left. |
| **What you left out** | The scope you cut, and why. |

## What the review agent found

Overnight, Larkspur pointed a review agent at your repository. It read the
code. It did not run your agent, and the only file it changed is this one. Each
item below names the file and the line it is about.

**1. search_alternatives description grew from 6 characters to 63 in agent.py, still shorter than every sibling tool.**

The diff changes the string from "search" to "Search available rebooking options for this disrupted itinerary", 63 characters. check_policy sits at 445 characters and lookup_booking at 283. Claude decides when to call search_alternatives off this one sentence, with no mention of what counts as an alternative or what fields come back.

Run python3 run.py --show-tools and paste the search_alternatives entry next to check_policy to compare what Claude is actually told.

**2. The loop fix in run_agent() now stores response.content instead of text_of(response) before the second API call.**

The diff replaces messages.append({"role": "assistant", "content": text_of(response)}) with messages.append({"role": "assistant", "content": response.content}), and moves answer = text_of(response) to after the follow-up call. This changes what gets sent back to the model as its own prior turn, from a stripped text string to the full content blocks including tool_use. The last committed trace shows 4 API turns and 3 tool calls in the order lookup_booking, get_flight_status, check_policy, which is consistent with the loop running, but nothing in the material isolates whether this specific edit changed the output on that run.

Run python3 bench.py --compare before after against a saved pre-diff trace to show what this change actually did to turns or tool_use fidelity.

**3. TONE_ADDENDUM is 0 characters and EXTRA_TOOLS is empty, so the shipped nine tools and default voice are all that ran.**

The static scan reports TONE_ADDENDUM at 0 characters and EXTRA_TOOLS declared: 0, with no LOCAL_TOOLS executors. The diff touches only two schema strings and the loop's message construction, nothing in the tone or tool-expansion seams. Whatever the agent said in the last run came entirely from SYSTEM_PROMPT plus the nine stock tool descriptions.

Run python3 run.py K7PQ2M --trace and check whether the system string includes anything beyond SYSTEM_PROMPT, since TONE_ADDENDUM is empty.

**4. No evals/cases.json exists, so the one committed run in readout-trace.json is the entire evidence base for this build.**

The trace shows 12729 input tokens against 693 output tokens, a wall clock of 14.3s, and 0 prompt-cache reads or writes with a hit ratio of None. Three tool calls ran: lookup_booking, get_flight_status, check_policy. A single trace cannot show whether that token ratio or that call order holds across booking shapes, disruption causes, or edge cases like the partner-segment or unaccompanied-minor paths that escalate_to_human's description names.

Run python3 eval_harness.py once a cases file exists and paste the totals so the 12729-to-693 token ratio has more than one data point behind it.

**5. Gates banked in readout.html are 1.3 and 1.4 only, out of the six pencil marks named in agent.py.**

The evidence block lists gates banked: 1.3, 1.4, banked by Ashish Garg. The six editable marks in agent.py cover steps 1.2, 1.3, 2.1 (twice), 2.2, and 4.1; the diff only touches material under 1.3 and 1.4's tool-schema and loop scope. Steps 1.2 (run_agent), 2.1 (EXTRA_TOOLS and LOCAL_TOOLS), 2.2 (tool_list), and 4.1 (TONE_ADDENDUM) show no banked gate and no corresponding diff line.

Run python3 verify.py 2.1 and python3 verify.py 4.1 to see whether those steps pass now or still show their starting state.

## Your four answers

The four lines under `## Priya asked` in your PITCH.md are still empty. They
are one line each and they are not a coding job: cost, what happens when it is
wrong, who runs it in June, and what you left out. Whoever on your side is not
editing agent.py is the right person to write them, and they are the four
things I will ask about first.

## Before our next meeting

> Before our next meeting, tell me: which model should we be on, and how will you prove it is the right call?
>
> Priya Raghavan, Larkspur Airlines

Bring two things. A recommendation, and the measurement behind it. If the model is
not the problem, say so, and bring the number that shows it.

## What this review read

- `agent.py (226 lines)`
- `PITCH.md (unchanged template)`
- `TEAM.md (unchanged template)`
- `readout-trace.json`
- `readout.html (evidence block)`

Reviewer: `claude-sonnet-5`. Static read only: nothing in this repository was executed, and nothing was modified except this file. Larkspur Airlines is a fictional training scenario. Confidential, do not distribute.
