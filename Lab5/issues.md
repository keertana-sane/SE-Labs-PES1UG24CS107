# Lab 5: Static Code Analysis — Hospital Patient System

**Name:** B S Keertana | **SRN:** PES1UG24CS107 | **File:** `hospital_patient_system.py`

Before: Pylint 5.19/10, Bandit 5 issues (2 High, 1 Medium, 2 Low), Flake8 11 warnings.
After: Pylint 10.00/10, Bandit 0 issues, Flake8 0 warnings.
Reports are in the `reports/` folder (`*_report_before.txt` and `*_report_after.txt`).

| Issue | Type | Line(s) | Description | Fix Approach |
|-------|------|---------|-------------|--------------|
| Shell command with string concatenation (`os.system`) — Bandit B605, **High** | Security | 56 | The filename is concatenated into a shell command, so a crafted filename can inject commands. | Removed `os.system`; use `print()` for the confirmation message. |
| Weak MD5 hash (Bandit B324, **High**) | Security | 45 | MD5 is broken and unsuitable for hashing patient identifiers. | Replaced with `hashlib.sha256`. |
| Use of `eval()` on a formula string (Bandit B307 **Medium**, Pylint W0123) | Security | 40 | Any string passed as `formula_expr` is executed as Python code (arbitrary code execution). | Parse with `ast.parse` and evaluate with a small whitelist evaluator (`+ - * /`, numbers, `base_bill` only). Other input raises `ValueError`. |
| `random.randint` for a ward token (Bandit B311, Low) | Security | 49 | Pseudo-random generator is predictable and not suitable for tokens. | Use `secrets.randbelow(9000) + 1000` (still a 4-digit token). |
| Hardcoded secret `nurse_access_key`, printed to the console (not flagged by any tool) | Security / Bug | 68, 77 | Master key is committed in source and written to output. | Read from environment variable `HOSPITAL_NURSE_KEY`; only print whether it is configured. |
| Bare `except: pass` (Bandit B110, Pylint W0702, Flake8 E722) | Bug | 22-25 | Hides every error; discharging an unknown patient silently does nothing. | Look up the record with `.get()`, return `False` if missing, else discharge and return `True`. |
| Mutable default argument `intake_log=[]` (Pylint W0102) | Bug | 13 | The same list is shared across all calls. | Default to `None` and create a new list inside the function. |
| Unused variables `bed_charge`, `base_bill`, `p` (Pylint W0612, Flake8 F841) | Quality | 29, 39, 61 | Dead code; `base_bill` was only meant to be referenced by the formula. | Removed `bed_charge`; `base_bill` is now passed to the evaluator; loop rewritten with `sum()`. |
| Unused import `math` (Pylint W0611, Flake8 F401) | Quality | 4 | Import never used. | Removed. Added `ast`, `operator`, `secrets` where needed. |
| File opened without `with` or encoding (Pylint R1732, W1514) | Bug / Quality | 53-55 | File is not closed if `write` raises; encoding is platform dependent. | `with open(filename, "w", encoding="utf-8") as ledger_file:`. |
| Comparison `== None` (Pylint C0121, Flake8 E711) | Style | 30 | Should use identity check. | Use `record is not None`. |
| Redundant if/else returning booleans (Pylint R1703, R1705) | Style | 32-35 | Four lines to return a boolean. | `return record is not None and record["is_admitted"]`. |
| Function parameter shadows global `patient_records` (Pylint W0621) | Quality | 59 | Hides the module-level variable. | Renamed parameter to `records`. |
| Function name `admitPatient` is not snake_case (Pylint C0103) | Style | 13 | Violates PEP 8 naming. | Renamed to `admit_patient` (call in `main` updated). |
| Missing module and function docstrings (Pylint C0114, C0116) | Style | 1, many | No documentation. | Added a docstring to the module and every function. |
| Old-style `%` formatting (Pylint C0209) | Style | 17 | Prefer f-strings. | Converted to an f-string. |
| Lines over 79 characters, missing final newline (Flake8 E501, W292; Pylint C0304) | Style | 7-9, 16, 72, 80 | Formatting violations. | Wrapped long lines and added the final newline. |
| `main()` runs on import | Quality | 80 | Importing the module would execute the demo. | Added `if __name__ == "__main__":`. |
| Floating-point noise in cost (`1200 * 1.12` = `1344.0000000000002`) — found by running the program | Bug | 41 | Output looked wrong. | `round(total, 2)` in `calculate_treatment_cost`. |

## Commits
1. `Fix Bandit findings: remove eval, shell call, MD5, weak RNG, silent except, hardcoded key`
2. `Fix Flake8 findings: unused import/variables, line length, None comparison, final newline`
3. `Fix Pylint findings: docstrings, snake_case, mutable default, f-string, with-open, naming, main guard`
