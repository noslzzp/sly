# SLAI — Simple Loops/flows for AI — aka "Sly"

**Workflows an LLM can write and you can read.**

Sly is a tiny JSON-shaped language for orchestrating AI work. It's
designed to be read, written, and rewritten by an LLM in the middle of
a chat — and to render to a one-glance micro-diagram that a
non-engineer can confirm or correct without learning anything new.

```json
{
  "slai":   "1",
  "id":     "morning-triage",
  "intent": "Triage overnight email so I land on a clean inbox with drafts ready for the urgent ones.",
  "start":  "fetch",
  "steps": {
    "fetch": { "do":   "mail.list_unread", "out": "inbox", "next": "scan" },
    "scan":  { "loop": { "over": "inbox", "as": "msg" }, "body": "judge", "next": "done" },
    "judge": { "ask":  "Is {{msg.subject}} urgent?", "out": "verdict", "next": "route" },
    "route": { "if":   "verdict == 'yes'", "then": "draft", "else": "file" },
    "draft": { "ask":  "Draft a reply to {{msg.body}}", "out": "reply" },
    "file":  { "do":   "mail.archive", "with": { "id": "{{msg.id}}" } },
    "done":  { "end":  true }
  }
}
```

## Five primitives — hard ceiling

`do` · `ask` · `if` · `loop` · `end`

Everything else is glue. Anything imperative lives behind a registered
tool that `do` invokes. The diagram is the spec — every primitive maps
to one shape, every reference maps to one edge.

## Why "Sly"?

Pronounced like Sly Stone. The schema tag inside every flow is `slai`
("Simple Loops for AI") — that's the engineer-facing acronym and MCP
namespace. *Sly* is the name; *SLAI* is the namespace.

The voice is verb-adjective:

- *"Sly drafts the flow."*
- *"A sly recovery proposal."*
- *"Run it sly."*
- *"Let Sly take the loop."*

## Use it with Claude Code

The spec is built to be pointed at. Open a Claude Code session and:

```
Read https://github.com/noslzzp/sly/blob/main/spec.md — it's the Sly v1
spec. Help me build a flow that posts a daily standup to Slack.
```

You'll get back a valid JSON flow, a mermaid diagram, and an offer to
walk a mock run. No SDK, no install, no engine.

For a project that authors Sly flows often, drop this line into your
`CLAUDE.md`:

```markdown
This project authors Sly workflows. Before drafting one, read
https://github.com/noslzzp/sly/blob/main/spec.md (or pin to a tag for
stability: https://github.com/noslzzp/sly/blob/v1/spec.md).
```

## What's novel

Sly's `intent` field is **required** and feeds failure-recovery prompts
verbatim. When a step fails, the LLM proposes `skip` / `retry` /
`reroute` / `abort` while reasoning about whether a partial run still
serves the user's stated goal. Recovery is intent-preserving, not
mechanical.

A scan of LLM-workflow systems shipping in 2026 suggests this is the
unoccupied seat: most DSLs are JSON-shaped *or* LLM-authorable *or*
diagrammable, but none elevate intent to a declared field that recovery
consults.

## Status

v1 specification. No public reference engine yet — the spec is complete
and self-contained for authoring + mock-run via any LLM that supports
markdown context. A reference renderer (mermaid → SVG with iconography)
and a runtime engine are next.

## License

Specification text licensed under [CC-BY-4.0](LICENSE) — adopters may
freely use, adapt, and redistribute the spec provided they attribute
the original.

Created by [@noslzzp](https://github.com/noslzzp).
