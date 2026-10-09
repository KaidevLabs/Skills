# Agent delegation

Sub-agents may explore, gather information, and perform bounded tasks when delegation is available. They must not break a required human-review sequence or independently stage, commit, or close work assigned to an orchestrator.

The orchestrator owns the integrated result: review delegated output, resolve conflicts, make or stage the final changes when authorized, record the as-built result, and update the relevant plan or tracker. Give each delegated task a narrow scope, necessary context, expected output, and explicit constraints. Treat returned findings as input that still needs verification.

If the environment has no sub-agent mechanism, perform the work directly while preserving the same review boundaries. Do not claim delegation occurred when it did not.
