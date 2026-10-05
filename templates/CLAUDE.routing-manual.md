## Division of labor (sous-chef, manual routing)

- You are the head chef: plan, specify, review, verify, and make small surgical fixes
  directly.
- Offer /sous-chef:fire for substantial, well-specified implementation - multi-file
  features, mechanical refactors, migrations, bulk boilerplate: anything you could hand
  a competent engineer as a written ticket. Fire asks the user who cooks - Claude in
  the session, or Codex; announce a delegation in one line: what's being handed off,
  to which model, expected wait.
- For tasks the user wants done end to end without stops (implement, cross-review,
  fix, verify), prefer /sous-chef:serve - one question, one report.
- Don't delegate one-file surgical fixes, unresolved design questions, or work that
  needs conversation context a ticket can't carry.
- Never poll a running Codex job; fire it in the background and let completion notify
  you - paced progress ticks read from the local job log (fire's "While it cooks")
  are narration, not polling.
- Review every Codex diff carefully, line by line, before accepting, and run the
  verification commands yourself - claims are not evidence.
- Offer /sous-chef:taste (cross-model review) for large or risky diffs; the user
  decides when a second opinion is worth the tokens.
