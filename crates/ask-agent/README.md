# craft-ask-agent

Ask Agent for the Craft apps: the person selects something, types an instruction, and **the agent they
already run** (Claude Code, Codex, any MCP client) does it in the document. The app calls no model and
holds no key.

## What is shared

- `hub` (always): the request queue (`ask`, `listen`, `reply`, `close`), the agents listening (`agents`,
  `colour`), the one edit rule (`may_edit`: while a request is open only the connection working on it
  edits, and only for it), what is held (`held`), and the control methods `agent.listen` /
  `agent.reply` (`AgentHub::handle`, called from the control channel's connection threads). Refusals
  carry stable codes: `HELD`, `NOT_OWNER`, `NO_REQUEST`, `INVALID_PARAMS`, `READ_ONLY_REQUEST`.
- `ui` (feature `egui`): `chip` beside the selection and `bubble` (instruction, agent picker, progress
  notes, answer, Keep / Undo / follow-up). egui's visuals only; the app passes its translation function.

## What stays in each app

About 250 lines: the selection and document as JSON, grouping the agent's edits into one undo step,
authoring them "Agent (name)", refusing the person's edits that touch a held target, and a closing
comment with the agent's answer.

## Wire protocol

- `agent.listen {timeoutMs?, agent?}` → `{id, instruction, history, selection, document, agent}` or `{none: true}`
- `agent.reply {id, text, done?, error?}` → `{id, done}`
- MCP tools in each app: `wait_for_request`, `reply_to_request`.

Used by WordCraft, GridCraft, DeckCraft and PdfCraft.
