+++
title = "Using Xcode with 9Router as Agent"
date = 2026-09-25T14:55:00+03:00
tags  = ["xcode", "agents", "9router"]
draft = false
+++
---

Xcode's coding intelligence now supports the **Agent Client Protocol (ACP)**, which means you're no longer locked into Apple's default model choices — you can register any ACP-compatible coding agent, including [Goose](https://goose-docs.ai/), and point it at whatever backend you want. Combine that with **9Router**, a self-hosted AI routing proxy, and you get a setup where Xcode can talk to dozens of models — with automatic fallback — through a single local endpoint.

This post covers why 9Router is worth adding to your setup, then walks through wiring it up as an Xcode agent via Goose.

## Why 9Router?

9Router sits between your coding tools and 40+ AI providers, exposing a single OpenAI-compatible endpoint (`http://localhost:20128/v1`). A few things make it worth running:

- **Format translation** — your tool speaks OpenAI's API format, 9Router translates it to whatever the underlying provider actually needs (Claude, Gemini, Vertex, GitHub Copilot, etc.). You don't have to care what format the model you're using expects.
- **Combos with fallback** — you can group several models into a named "combo" with a routing strategy (round robin, priority fallback, or even fusion, where multiple models are queried in parallel and a judge model picks the best answer). Point your tool at the combo name once, and 9Router handles switching models when one hits a rate limit or fails.
- **Quota tracking and token savings** — a dashboard shows consumption per provider, and 9Router's token-saving layer trims tool-output noise (like `git diff` or `ls` output) before it reaches the model, which adds up fast in agentic workflows.
- **Multi-account support** — round-robins across multiple accounts/keys for the same provider, so you're not capped by a single subscription's quota.

In short: instead of hardcoding one model into every tool you use, you configure things once against 9Router and control routing centrally.

## Setting Up Goose as an Xcode Agent

Xcode's Intelligence settings let you register a custom agent that speaks ACP. Goose has native ACP support, so it's a natural fit.

**1. Install Goose** and confirm the binary path:
```bash
> brew install block-goose-cli
> which goose
```

**2. Register the agent in Xcode**
Go to **Xcode → Settings → Intelligence → Agents → Add an Agent**, and fill in:

| Field | Value |
|---|---|
| Name | `Goose (via 9Router)` |
| Executable | `/opt/homebrew/bin/goose` |
| Arguments | `acp` |

The `acp` argument is what starts Goose in ACP server mode over stdio — this is the part that's easy to get wrong, since it's not the same as `agent` or any other subcommand name you might guess.

**3. Add environment variables** so Goose routes through 9Router instead of OpenAI directly:

| Key | Value |
|---|---|
| `GOOSE_PROVIDER` | `openai` |
| `GOOSE_MODEL` | your model or combo name, e.g. `my-fallback-combos` |
| `OPENAI_API_KEY` | your 9Router key |
| `OPENAI_HOST` | `http://localhost:20128/v1` |

A couple of notes that took some trial and error:
- Goose's OpenAI-compatible provider expects **`OPENAI_HOST`**, not `OPENAI_BASE_URL` — an easy variable name to get wrong if you're used to other tools' conventions.
- `GOOSE_MODEL` only accepts a single model name — it's not a list. If you want fallback across multiple models, that logic has to live on 9Router's side (as a combo), not in Goose's config.

**4. Test in terminal first.** Before wiring it into Xcode, confirm the whole chain works from a plain terminal session:
```bash
GOOSE_PROVIDER=openai GOOSE_MODEL=my-fallback-combos \
OPENAI_API_KEY=sk-... OPENAI_HOST=http://localhost:20128/v1 \
goose session
```
If you can chat here, the 9Router connection and model resolution are solid, and Xcode should connect without issue. (Don't test with `goose acp` directly in a terminal — it just waits silently for JSON-RPC on stdio, which looks broken but isn't.)

**5. Save the agent in Xcode** and select it from the coding assistant panel.

## Wrap-up

Once this is wired up, switching models is just a matter of editing a combo in 9Router's dashboard or duplicating the agent entry in Xcode with a different `GOOSE_MODEL` — no code changes, no re-authenticating each provider separately. It's a small amount of setup for a lot of flexibility.

---