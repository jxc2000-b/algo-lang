# algo-lang Roadmap

## v1 — Foundation (Current)

### ✅ Core Features
- **YAML-based syntax** — Human-readable algorithm definitions
- **Algorithmic versioning** — Semantic versioning with changelogs, previous version tracking, and deprecation flags
- **Built-in primitive types** — `int`, `float`, `string`, `bool`, `array`, `map`
- **Custom primitive types** — Define new algorithmic primitives with base types, properties, and constraints
- **Algorithm metadata** — Author, description, tags, complexity annotations
- **Input/output specifications** — Strongly-typed algorithm interfaces
- **Step-by-step definitions** — Ordered steps with control flow (`if`, `elif`, `else`, `while`, `for`, `return`)
- **YAML schema** — Validation schema for algorithm files

---

## v2 — Composition & Ecosystem (Planned)

### 🔲 Algorithmic Stacks
Compose multiple algorithms together into pipelines and workflows:
```yaml
stack:
  name: search-and-sort
  version: 1.0.0
  description: "Sort an array then search for a value"
  pipeline:
    - algorithm: bubble-sort
      version: ">=1.0.0"
      inputs:
        arr: stack.input.arr
      outputs:
        sorted_arr: -> next.arr
    - algorithm: binary-search
      version: ">=1.0.0"
      inputs:
        arr: previous.sorted_arr
        target: stack.input.target
      outputs:
        index: -> stack.output.result
```

### 🔲 Algorithm Registry & Imports
Import and reference algorithms from external sources:
```yaml
imports:
  - name: quick-sort
    source: algo-registry/sorting
    version: "^2.0.0"
  - name: hash-map
    source: ./local/data-structures/hash-map.algo.yaml
```

### 🔲 Conditional & Parallel Stacks
Support branching and parallel execution within stacks:
```yaml
stack:
  pipeline:
    - parallel:
        - algorithm: merge-sort
        - algorithm: quick-sort
    - select:
        strategy: fastest
```

### 🔲 Testing & Assertions
Define test cases directly in algorithm files:
```yaml
tests:
  - name: "finds element in middle"
    inputs:
      arr: [1, 3, 5, 7, 9]
      target: 5
    expected:
      index: 2
  - name: "element not found"
    inputs:
      arr: [1, 3, 5, 7, 9]
      target: 4
    expected:
      index: -1
```

### 🔲 Visualization Hints
Add rendering hints for algorithm visualization tools:
```yaml
visualization:
  type: step-through
  highlight:
    - step: 3
      variables: [mid, low, high]
  layout: tree
```

### 🔲 Complexity Verification
Automated complexity analysis and verification against declared bounds.

---

## Future Ideas (Unscheduled)

- **Algorithm diffing** — Visual diffs between algorithm versions
- **Code generation** — Generate executable code from algo-lang definitions (Python, JavaScript, etc.)
- **Interactive playground** — Web-based editor and step-through debugger
- **Algorithm similarity detection** — Find similar algorithms across the registry
- **Formal verification** — Prove correctness properties of defined algorithms
- **Natural language descriptions** — Auto-generate human-readable explanations from algorithm definitions

---

## Contributing

Have an idea for a feature? Open an issue with the `enhancement` label!