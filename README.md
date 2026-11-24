# Workflow Satisfiability Problem (WSP) Multi-Method Solver

A self-contained Python solver and empirical study for the **Workflow Satisfiability Problem (WSP)**, developed for **COMP3070 Symbolic Artificial Intelligence (2026/2027 Autumn, Group L)**.

The solver supports all five standard WSP constraint types—**Authorisations**, **Binding-of-Duty**, **Separation-of-Duty**, **At-Most-$k$**, and **One-Team**—and classifies every instance as `unsat`, `sat` (unique solution), or `sat` (multiple solutions), with independent witness verification on every returned plan.

## Solving Methods

All three mathematically equivalent formulations are unified in `wsp_solver.py`:

| Method (`--method`) | Formulation | Backend | Key Characteristics |
| :--- | :--- | :--- | :--- |
| `cp-sat` *(default)* | Boolean assignment matrix (B1–B7) | OR-Tools CP-SAT | Strongest scaling reliability on larger/unseen instances; supports multi-worker search. |
| `smt` | Finite-domain integer assignment (I1–I7) | Z3 | Compact primary variable encoding ($n$ integer variables) with `ite` cardinality sums. |
| `pattern` | Canonical step partitions + bipartite matching (P1–P7) | Pure Python (stdlib) | Adapts Karapetyan et al. (2019) to support user-dependent `one-team` constraints via single-pass domain tightening, mid-search pigeonhole pruning, incremental team backtracking, and edge-forbidding rematching. |

## Verification & Benchmark Highlights

- **Correctness**: 100% pass rate across **324** targeted checks (official Examples 1–8 plus 100 seeded random workflows verified against brute-force enumeration) and **100% cross-method agreement** across **537** benchmark runs covering **179** unique instances.
- **Small & Multi-Constraint Workflows ($n \le 10$)**: `pattern` achieves sub-millisecond runtimes—averaging **0.16 ms** on `5-constraint` ($n=10, m=50$), ~**415×** faster than `cp-sat` (67.20 ms) and ~**707×** faster than `smt` (114.46 ms).
- **Larger Workflows ($n = 20\text{–}60$)**: `cp-sat` overtakes `pattern` on `example11`/`example12` ($n=20, m=100$) and solves large 40–60 step instances (`example16`, `example17`, `example18`) under extended 300–600 s budgets.

## Repository Structure

```text
.
├── wsp_solver.py                                   # Unified single-file WSP solver (CP-SAT, SMT, Pattern)
├── COMP3070_Coursework_2_Final_Report_(Group_L).pdf # Full mathematical formulations & empirical report
├── requirements.txt                                # Python dependencies
└── README.md