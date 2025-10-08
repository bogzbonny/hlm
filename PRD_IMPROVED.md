**## Overview**  
The Heuristic Language Model (HLM) is a lightweight system that emulates the behavior of a large language model (LLM) within a narrowly‑defined domain of question‑and‑answer patterns. It consists of a Rust library **and** a command‑line binary. The binary is responsible for generating configuration files, example data, parsing templates, HLM definition files, and reporting success‑rate statistics. All matching logic is based purely on deterministic heuristics (regular expressions, string similarity, etc.)—no neural inference is performed.

**## Goals**  
- Deliver a combined **library + binary** package for HLM development and execution.  
- Automatically **generate a diverse set of example requests** (e.g., calendar entries) and their correct parsed representations.  
- Produce **templates** that link each example to the heuristic that should capture it.  
- Create **“.hlm” files** containing success‑rate metadata and failure logs.  
- Maintain a **ranking of HLM files** based on their observed success rates.  
- Keep the entire implementation **pure Rust‑only** and **heuristic‑driven** (no ML inference).  

**## Scope**  
**In‑scope**  
- Rust library exposing the heuristic matching engine.  
- CLI binary with sub‑commands for:  
  - Initializing a config file (`hlm_gen_config.toml`).  
  - Generating a large set of example inputs (`hlm_examples.toml`).  
  - Producing the correct parsed output for each example.  
  - Adding the originating template to each example entry.  
  - Writing HLM definition files (`hlms/my-hlm‑<hash>.hlm`) that embed success‑rate statistics.  
  - Listing all HLM files ordered by success rate.  
- Use of the **`textdistance.rs`** crate for similarity calculations (e.g., Jaro‑Winkler, Levenshtein).  
- Use of the **`ollama‑rs`** crate **exclusively** for any LLM‑based generation required (e.g., creating example sentences).  

**Out‑of‑scope**  
- Integration with any LLM providers other than Ollama.  
- Inclusion of non‑Rust crates for matching (e.g., Python‑based regex engines).  
- Persistence mechanisms beyond simple file‑based caches (no databases).  
- Runtime heuristic learning or adaptive model updates.  

**## Requirements**  

1. **Library / Binary Duality**  
   - Provide a Rust crate (`hlm`) that can be imported as a library.  
   - Ship an executable (`hlm`) that exposes the required sub‑commands.  

2. **Configuration Initialization**  
   - `hlm init` creates a default `hlm_gen_config.toml` with sensible defaults.  

3. **Example Generation**  
   - `hlm generate-examples` produces a `hlm_examples.toml` containing at least **10 distinct calendar‑entry requests** in varied natural‑language styles.  
   - Each entry must be a plain string (no surrounding JSON) and represent a realistic user utterance.  

4. **Correct Output Production**  
   - For every example, generate the **expected parsing result** (phrase, time, date).  
   - Store this result alongside the example in `hlm_examples.toml`.  

5. **Template Generation**  
   - Automatically derive an **input template** for each example (e.g., `"[phrase] [time] [date]"`).  
   - Append the template to the example entry for traceability.  

6. **HLM File Creation**  
   - `hlm build` writes `hlms/my-hlm-<hash>.hlm` files.  
   - Each file contains:  
     - The heuristic definitions (regexes, similarity thresholds).  
     - Metadata: success count, failure count, overall success rate.  

7. **Success‑Rate Listing**  
   - `hlm list` displays all `.hlm` files sorted descending by success rate.  

8. **Similarity Calculation**  
   - When an input does **not** match any exact regex, compute word‑level similarity using `textdistance.rs`.  
   - Prefer fast algorithms (Jaro‑Winkler, Levenshtein) and fall back to slower ones only if needed.  

9. **Bulk Generation via Ollama**  
   - All LLM‑driven content (example sentences, correct parsing JSON, templates) must be generated **solely** with the `ollama‑rs` crate.  
   - No other LLM APIs or services may be invoked.  

10. **Heuristic Verification & Caching**  
    - Maintain a file‑based cache of previous LLM evaluations to avoid redundant calls.  
    - For each heuristic, record:  
      - Whether it was used.  
      - Success/failure outcomes per example.  

11. **Ordering Optimization**  
    - Implement an iterative algorithm that:  
      - Randomly shuffles heuristic ordering.  
      - Executes the full example suite, logging per‑heuristic success percentages.  
      - Adjusts ordering to push successful heuristics forward and reduce overall failure rate.  
      - Terminates after either 100 % success or after 10 unsuccessful re‑ordering passes, selecting the best‑performing ordering.  

12. **Logging Requirements**  
    - Log:  
      - Percentage success per ordering.  
      - Heuristics never triggered.  
      - Examples that failed and the reason (no match, wrong match).  

13. **Edge‑Case Handling**  
    - Support phrases like:  
      - `"add to my work calendar: team sync on 2024‑05‑01 at 14:00"`  
      - `"create a new calendar named holidays"`  
    - Preserve any user‑provided time zones or relative date expressions (`"next friday"`, `"tomorrow"`).  

14. **Code Style**  
    - All Rust code must compile with `cargo check` using the stable toolchain.  
    - Follow `rustfmt` default formatting.  

**## Acceptance Criteria**  

- Running `hlm init` creates a valid `hlm_gen_config.toml` in the current directory.  
- `hlm generate-examples` outputs a `hlm_examples.toml` that contains **exactly 10** JSON‑compatible example strings, each paired with a correct parsing object and an auto‑generated template.  
- The JSON parsing objects match the structure:  

  ```json
  {
    "function": "add_to_calendar_relative",
    "parameters": {
      "phrase": "sports team general meeting",
      "time": "5pm",
      "date": "next friday"
    }
  }
  ```  

- `hlm build` produces at least one `.hlm` file whose filename includes a SHA‑256 hash and whose header includes a success‑rate field (e.g., `success_rate = 0.92`).  
- `hlm list` displays all `.hlm` files ordered by descending success rate, and the top file achieves **≥ 95 %** success on the full example suite.  
- For any example that does not match an exact regex, the similarity module returns a match with a similarity score ≥ 0.85 and the correct parsing is produced.  
- All LLM calls (example generation, parsing output, template creation) are performed via the `ollama‑rs` crate; logs confirm no other LLM client was used.  
- The heuristic ordering algorithm terminates after either perfect accuracy or after 10 re‑ordering attempts, and the final log file documents the chosen ordering and its success percentage.  
- Cache files are created and reused on subsequent runs, reducing LLM calls by at least **50 %** on a second execution of `hlm generate-examples`.  
- Edge‑case inputs described in the requirements are correctly parsed and logged as successes.  
- The repository builds without warnings (`cargo build --quiet`) and passes `cargo test` (if any tests are added).  

Meeting all the above criteria constitutes a complete, well‑structured PRD ready for implementation.
