# QuantumSecurity

**Educational, runnable implementations of quantum algorithms that matter for cryptography and cybersecurity analytics — built on Qiskit + Aer, verified end-to-end, and honestly scoped.**

> Every notebook here runs on a laptop (the Aer statevector simulator) and is deliberately a *toy*: it demonstrates the **mechanism** of an attack or analysis at a scale a classical simulator can reach. None of it breaks, or gives meaningful uplift against, any real-world cryptosystem. Where a real target is mentioned, the gap to it is stated explicitly.

---

## Scope — what gets implemented here

This repository has **one rule** about what we add code for:

> **An algorithm is implemented here if and only if it serves cryptography or cybersecurity analytics.**

Quantum computing has a large algorithm landscape (chemistry, materials, finance, topology…). Most of it is fascinating and *out of scope* for this repo. We map the whole landscape below so the picture is complete, but we only ship working notebooks for the security-relevant ones, and we point everything else to the [Quantum Algorithm Zoo](https://quantumalgorithmzoo.org/).

**Status legend**

| Symbol | Meaning |
|---|---|
| ✅ | Implemented and tested in this repo |
| 🟡 | In scope (security-relevant) — implementation planned / on request |
| 🔵 | Out of scope here — see the [Quantum Algorithm Zoo](https://quantumalgorithmzoo.org/) |

---

## What's here now

| Notebook | Algorithm | What it shows | Status |
|---|---|---|---|
| `Shor_Factoring_N21.ipynb` | Shor (factoring) | Reproduces Skosana & Tame (2021): compiled order-finding factors 21 → 3 × 7 | ✅ |
| `Shor_Factoring_N143.ipynb` | Shor (factoring) | Compiled, orbit-encoded factoring of 143 → 11 × 13 | ✅ |
| `Shor_ECDLP_Toy.ipynb` | Shor (discrete log) | Elliptic-curve discrete log on a toy curve — recovers a secret scalar | ✅ |
| `Grover_Toy_AES_KeySearch.ipynb` | Grover | Key recovery on a 4-bit AES-style SPN cipher from a known plaintext/ciphertext | ✅ |
| `Grover_Hash_Collision_BHT.ipynb` | Grover (single-anchor) | A genuine hash collision via amplitude amplification *(building block of BHT, not full BHT — see notes)* | ✅ |
| `Grover_Hash_Preimage_Optimal.ipynb` | Grover + BBHT | Preimage search, including the realistic unknown-solution-count case; with the BBBV optimality argument | ✅ |

---

## A gradual tour of quantum algorithms (and which ones we cover)

The aim of this section is to read top-to-bottom as a learning path: from the simplest "hello world" circuits, through the two algorithms that actually threaten cryptography, out to the wider toolbox — flagging security relevance and implementation status at each step.

### Level 0 — Foundations (the building blocks)

These prove the *principle* that quantum interference can beat classical query complexity. They're rarely useful on their own but underpin everything later.

- **Deutsch–Jozsa / Bernstein–Vazirani** — distinguish constant vs balanced functions / recover a hidden bit-string in one query. *Pedagogical.* 🟡 *(useful as a teaching on-ramp)*
- **Simon's algorithm** — finds a hidden XOR-period with an exponential speedup; the direct inspiration for Shor. *Security relevance:* underlies quantum distinguishers against certain symmetric modes and MACs (e.g. Even–Mansour, some CBC-MAC variants) in the quantum-query model. 🟡

### Level 1 — The two cryptographic pillars

This is the heart of the repo.

- **Shor's algorithm** — factoring and discrete logarithms in polynomial time. *Security relevance:* breaks **asymmetric** cryptography outright — RSA, Diffie–Hellman, and elliptic-curve schemes (ECDH/ECDSA). This is the existential threat behind post-quantum cryptography migration. ✅ *(factoring 21 & 143, plus elliptic-curve discrete log)*
- **Grover's algorithm** — unstructured search with a quadratic speedup. *Security relevance:* halves the effective security of **symmetric** primitives — block-cipher key search and hash preimages. The standard response is simply to double key/digest length (AES-256, SHA-512). ✅ *(toy-AES key search, hash preimage)*

> Together these define the threat model: Shor is the catastrophe (exponential, kills public-key crypto); Grover is the manageable dent (quadratic, mitigated by bigger parameters).

### Level 2 — The families around the pillars

Generalizations and cousins of Shor and Grover, several directly security-relevant.

- **Quantum Phase Estimation (QPE)** — estimates eigenphases of a unitary; the engine *inside* Shor. *Security relevance:* the order-finding core of every Shor implementation. 🟡 *(currently embedded in the Shor notebooks; a standalone version is planned)*
- **Hidden Subgroup Problem (HSP)** — the abstract frame containing factoring, discrete log, and period-finding. The non-abelian case (open) would bear on lattice and graph-isomorphism problems. 🔵 *(theory; see the Zoo)*
- **Amplitude Amplification & Estimation / Quantum Counting** — generalize Grover; give quadratic speedups for Monte-Carlo estimation and counting. *Security relevance:* quantitative risk/analytics, and estimating the number of keys/preimages before a search. 🟡
- **Quantum walks — element distinctness, collision finding** — $O(N^{2/3})$ collision search; the algorithmic family behind **BHT**. *Security relevance:* hash collision resistance. 🟡 *(the full table-based BHT is the natural next step beyond the single-anchor collision notebook)*

### Level 3 — The broader toolbox

Powerful, but most are *out of scope* unless they feed a security or analytics use case.

- **Hamiltonian simulation** (Trotter, LCU, qubitization, QSVT) — simulate quantum systems. *The most defensible exponential speedup,* but for chemistry/materials/physics. 🔵
- **HHL & Quantum Singular Value Transformation (QSVT)** — linear systems and a unifying meta-algorithm from which Grover, QPE, and Hamiltonian simulation all derive. *Security relevance:* only via downstream ML/analytics, and subject to strong assumptions (state preparation, QRAM). 🟡 *(conditional — only if it powers a concrete cybersecurity-analytics task)*
- **Quantum machine learning** (quantum kernels, variational classifiers) — *Security relevance:* anomaly / intrusion / malware-family detection and similar analytics. Speedups are largely unproven and case-dependent. 🟡 *(in scope when tied to a cybersecurity-analytics problem)*
- **Variational / optimization (VQE, QAOA, annealing)** — VQE targets chemistry (🔵); QAOA targets combinatorial optimization, which *can* include security-flavoured problems (e.g. constraint/assignment optimization). 🟡 *(QAOA conditional on a security use case)*
- **Specialized results** — Jones-polynomial approximation (BQP-complete), topological data analysis, differential-equation and SDP solvers, glued-trees walk, Yamakawa–Zhandry (2022). 🔵 *(see the Zoo)*

---

## Honest caveats (read before drawing conclusions)

- **Toy scale only.** These run at ~10–20 qubits. Real targets need thousands to millions of fault-tolerant qubits (e.g. ECDLP-256 ≈ 2,330 logical qubits; AES-128 key search ≈ 2⁶⁴ Grover iterations; SHA-256 preimage ≈ 2¹²⁸). Statevector simulation hits a wall around 26–28 qubits — the very wall that protects real cryptography.
- **Robust speedups are narrow.** Provable, assumption-light exponential speedups live mainly in number theory (Shor) and physics simulation. Many linear-algebra / ML speedups depend on QRAM and efficient state preparation, and some have been *dequantized* (Tang et al.). NISQ variational methods have no proven speedup yet.
- **Preimage vs collision asymmetry.** Grover is *provably optimal* for preimage (BBBV lower bound, $2^{n/2}$). Quantum collision finding (BHT, $2^{n/3}$) needs large QRAM and often loses to classical parallel collision search — so collision resistance degrades *less* under quantum than naïve intuition suggests.

---

## Requirements & running

```bash
pip install qiskit qiskit-aer pylatexenc matplotlib
```

Open any notebook in Jupyter or Google Colab and run top-to-bottom. Each notebook installs its own dependencies in the first cell, verifies its quantum circuit against a classical reference, draws the circuit, and reports results.

---

## Further reading

The canonical, citation-backed catalogue of quantum algorithms is the **Quantum Algorithm Zoo**, maintained by Stephen Jordan:
**https://quantumalgorithmzoo.org/**

It lists dozens more algorithms with precise speedups and source papers. This repo intentionally implements only the security-relevant subset.

---

## License

Released under the **MIT License** (see [`LICENSE`](LICENSE)).

MIT permits reuse, modification, and redistribution **on the condition that the copyright and permission notice — i.e. the attribution to the authors — is retained in all copies or substantial portions.** In plain terms: *use it freely, but keep our names on it.*

### Attribution & citation (please)

The MIT notice covers code reuse. For **academic or published work** that uses these implementations or ideas, we additionally ask that you **cite the repository** (see [`CITATION.cff`](CITATION.cff)):

> Tehrani, M. (Mark) (2026). *QuantumSecurity: educational quantum-algorithm implementations for cryptography and cybersecurity analytics.* GitHub: https://github.com/owlmt/QuantumSecurity

If MIT's attribution requirement is not sufficient for your context and you need a stronger explicit-attribution license (e.g. a NOTICE-file requirement), Apache-2.0 is a drop-in alternative — open an issue and we can dual-license.

---

## Contributing

Pull requests are welcome **within scope** (cryptography or cybersecurity analytics). A good contribution: a small, Aer-runnable notebook that verifies its quantum result against a classical reference, draws its circuit, and states honestly how far it sits from any real-world target. Out-of-scope algorithms are better contributed to general-purpose collections; we'll happily link them.

## Disclaimer

These are **educational and research** artifacts for understanding the quantum threat model and quantum-assisted security analytics. They provide no practical capability against deployed cryptographic systems and must not be represented as doing so.
