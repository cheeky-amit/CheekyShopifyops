# Example — composing Shopify skills with an email MCP

You have `shopify-skills` installed. You also have an email MCP (any of: Klaviyo, Mailchimp, Resend, …). You want to send a win-back email to lapsed customers.

## What you do NOT do

You do NOT add a `retention.send-winback-email` skill to `shopify-skills`. That would couple the bundle to one email vendor and break its strict-MCP scope.

## What you do instead

### Option 1 — chain at the conversation level

```
> Merchant: "Build a win-back list."
   (uses retention.win-back-list — produces the cohort)

> Merchant: "Send a 15% off email to that list."
   (uses your email MCP's "create campaign" + "send" skills)
```

The host routes each prompt to the right MCP. The merchant's words bridge them.

### Option 2 — write a project-specific composite skill

If this is a recurring workflow, write the composite in *your* project repo (NOT in `shopify-skills`). For example, in your project's `skills/mybrand/winback-cycle/SKILL.md`:

```yaml
---
id: mybrand.winback-cycle
name: winback-cycle
title: Build win-back list and prepare campaign
risk: write           # writes to email MCP
mcp_tools:            # multiple MCPs — only legal in a project bundle
  - <shopify direct tools used>
  - <email MCP tools used>
---
```

Workflow:
1. Call `retention.win-back-list` (read).
2. Pass the cohort to your email MCP's "create draft campaign" tool.
3. Apply the safety contract — preview the campaign with subject, body, and audience size; confirm; send; log.

The composite lives in your project, not in `shopify-skills`. Pulling updates to `shopify-skills` stays clean.

## The pattern, generalized

| Shopify side                | External side                  | Bridge        |
|-----------------------------|--------------------------------|---------------|
| Read / build list           | Send / write / notify          | Merchant's prompt OR project-specific composite skill |
| Stays strict-MCP            | Lives in its own bundle        | The composite (if any) lives in *your* project |

This shape works the same with Slack ("post stuck-order alert"), Notion ("write weekly brief to a page"), BigQuery ("export sales fact table"), and so on.
