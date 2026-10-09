---
name: tdd
description: Implement behavior through small red/green vertical slices at meaningful test seams, with a test that proves the behavior before production code. Use when changing behavior and the repository has an applicable automated test setup.
---

# Test-driven development

Use the target repository's documented test framework, conventions, and commands. Read its contribution and testing guidance before starting. This skill describes the development loop; repository rules determine exact paths, fixtures, commands, and test layers.

## Red/green loop

1. Identify the behavior and the narrowest public or architectural seam that exposes it. Prefer a test at the layer where a consumer observes the behavior.
2. Write one failing test for a concrete, externally meaningful requirement or failure case. Run it and confirm it fails for the intended reason, not due to setup or a syntax error.
3. Make the smallest production change that makes that test pass. Do not add speculative abstractions or unrelated cleanup in the loop.
4. Run the focused test again. If it passes, add the next behavior as another failing test and repeat.
5. Once the required behaviors pass, run the broader relevant suite and the repository's required static checks. Refactor only after a green state, keeping the suite green.
6. Report the exact commands and results, including checks that were unavailable or blocked.

## Test quality

- Assert behavior at a stable seam rather than private implementation details, incidental query counts, or exact internal call sequences unless they are part of the contract.
- Make each test fail when the behavior is absent or wrong. Avoid tautological assertions, tests that merely restate the implementation, and excessive mocking that prevents the behavior from running.
- Cover meaningful boundaries, invalid input, authorization, and failure handling when required by the specification; avoid duplicating the same assertion at every layer.
- Use the application's established factories, fixtures, and helpers. Do not introduce project-specific test structure or commands as universal rules.

If a behavior cannot be tested automatically in the project's available setup, explain the gap and use the closest reliable verification method without describing it as a passing automated test.
