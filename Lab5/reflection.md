# Lab 5 Reflection — B S Keertana (PES1UG24CS107)

**1. Which issues were the easiest and which were the hardest to fix?**
Style issues (docstrings, snake_case, line length, `== None`) were mechanical and quick. The hardest was `eval()`: simply deleting it would break `calculate_treatment_cost`, so I had to replace it with an `ast`-based evaluator that only allows numbers, `base_bill` and basic arithmetic, and confirm it still gives the same answer for `base_bill * 1.12`.

**2. Did the tools report any false positives, or miss anything?**
Nothing was clearly a false positive, but some warnings were low value for this program (for example Bandit's B311 on a ward token that is only a demo). The tools also missed things: the hardcoded `HOSPITAL_NURSE_MASTER_KEY_2026` was never flagged and was even printed to the console, and the float result `1344.0000000000002` only showed up by running the program. Static analysis does not replace running and reading the code.

**3. How could static analysis be integrated into a development workflow?**
Run Flake8, Pylint and Bandit as a pre-commit hook and as a required CI check on every pull request, with Bandit High/Medium findings blocking the merge and a minimum Pylint score (such as 9.5). That catches problems before review and keeps reviewers focused on logic.

**4. What tangible improvements did you see?**
Pylint went from 5.19 to 10.00, Bandit from 5 issues (2 High) to 0, and Flake8 from 11 warnings to 0. The code is also safer (no shell injection, no arbitrary code execution, no weak hash or hardcoded secret), easier to read and reuse (docstrings, `main` guard), and more predictable (no shared mutable default, no silently swallowed errors).
