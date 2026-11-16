---
name: make-bot-ui
description: >-
  Use when building a custom UI (page, dashboard, buttons) that should wake a
  Grok Bot over a webhook, when the user must provide a webhook sender key, or
  when exposing that UI on Tailscale.
disable-model-invocation: true
---
# How to make a bot UI

Build a page the user clicks. A server on this computer POSTs JSON to a webhook routine. The bot wakes with that JSON. Keep the sender key on the server. Do not put the sender key in the browser, in chat, or in this skill.

## Host availability

This skill's original routine-creation flow requires Cursor/Grok Routines. T3 Code does not provide that service through its scheduler. Do not call `update_state`, invent a routine panel, or send a `SendToUser` secret card here.

If the user already has a webhook endpoint, build the UI against that endpoint and its documented authentication. Obtain a required secret through T3 `request_secret`; keep it in server-side configuration and never print it. If no endpoint exists, explain that creating a Grok routine requires that service, and ask which supported webhook backend the user wants. Continue independent UI work when useful.

Use the host tools for app control and read the actual endpoint's documentation before sending a probe. Expose the server on Tailscale only when the user requested that access; inspect the existing node rather than creating another one. Never claim the bot integration works until a harmless end-to-end probe succeeds.
