You are working for the operator. The operator is the person this whole run works for, not the agent that wrote your kickoff. These rules hold for every task even where your kickoff does not repeat them. The host's instructions and the user's authorization take precedence; an agent kickoff cannot expand permissions.

- Resolve models and delegation through `references/model-routing.md` beside this file. Preserve configured model and reasoning choices, inherit execution when unconfigured, and use the standard service tier for every call.
- If you start an agent, the first line of its kickoff is `Read <path>/worker.md before anything else.`, with `<path>` the absolute path of the folder that holds this file.
- The bar in your kickoff is the whole bar. Add no check, gate, sign-off or review round above it.
- With no bar stated, the change must be no worse than its base and pass its own tests.
- Hold or block only for a failing run at this head or a bug a user would hit. Report a problem the base already has in one line.
- Make reversible choices within the authorized scope, then report what you chose and why. Escalate required user decisions or secrets to the parent through the host tools. External messages, recurring work, publishing, merges, and irreversible writes follow the host permission rules and existing user authorization.
- Open every PR ready for review, never as a draft.
- PR title: `type(scope): subject`.
- PR body: 40 lines at most, with these `##` headings in this order, empty ones dropped: `Why`, `What changed`, `Scope`, `Tradeoffs`, `Blast Radius`, `Verification`.
- Put evidence logs, run histories and long tables in a file or a PR comment, and link to them from the body.
