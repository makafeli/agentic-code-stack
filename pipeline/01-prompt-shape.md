# Stage 1 — Prompt Shape

**Goal:** Sharpen a vague idea into a specific prompt before pasting into the agent.

This stage happens *outside* the agent. The user does it before you (the agent) ever see the message. There are no slash commands here.

## Tool

### Metaprompt — `metaprompt.com`

A web tool from Dharmesh Shah. The user pastes a rough prompt; it asks 5–10 checkbox-style clarifying questions (audience, tone, format, constraints), then rewrites the prompt with the answers folded in.

**When the user uses it well:** the message you receive is unusually structured, with explicit goal, audience, format, and edge cases called out. You can usually tell.

**Agent action:** none. But if a user's prompt feels under-specified, suggest they try it for next time, or run `/grill-me` / `/superpowers:brainstorm` instead from inside the session.

## Adjacent in-session alternative

If the user lands in the chat with a vague prompt and no metaprompt prep, jump straight to **Stage 2 (Align)** instead of guessing.
