# algo-lang

A YAML-based algorithmic description language for defining, versioning, and sharing algorithms.

## Overview

**algo-lang** is a domain-specific language (DSL) built on YAML that provides a structured, human-readable way to define algorithms. It supports algorithmic versioning, custom primitive types, and a clear specification format that makes algorithms easy to understand, maintain, and share.

## Features (v1)

- **YAML-based syntax** — Familiar, readable format for defining algorithms
- **Algorithmic versioning** — Track changes to algorithms over time with semantic versioning, changelogs, and diff support
- **Built-in primitive types** — `int`, `float`, `string`, `bool`, `array`, `map`, and more
- **Custom primitive types** — Define your own algorithmic primitives to extend the language
- **Algorithm metadata** — Author, description, tags, complexity annotations
- **Input/output specifications** — Strongly-typed algorithm interfaces
- **Step-by-step definitions** — Clear, ordered algorithmic steps with control flow

## Quick Start

### Example Algorithm Definition

```yaml
algorithm:
  name: binary-search
  version: 1.0.0
  description: "Search for a target value in a sorted array"
  author: "jxc2000-b"
  tags: [search, divide-and-conquer]
  complexity:
    time: O(log n)
    space: O(1)

types:
  primitives:
    - int
    - bool
  custom:
    - name: sorted-array
      base: array
      constraints:
        - ordered: ascending
        - element_type: int

inputs:
  - name: arr
    type: sorted-array
    description: "A sorted array of integers"
  - name: target
    type: int
    description: "The value to search for"

outputs:
  - name: index
    type: int
    description: "Index of the target, or -1 if not found"

steps:
  - set:
      low: 0
      high: length(arr) - 1

  - while:
      condition: low <= high
      do:
        - set:
            mid: (low + high) / 2
        - if:
            condition: arr[mid] == target
            then:
              - return: mid
        - elif:
            condition: arr[mid] < target
            then:
              - set:
                  low: mid + 1
        - else:
            - set:
                high: mid - 1

  - return: -1
```

## Algorithmic Versioning

algo-lang includes built-in versioning support so you can track how algorithms evolve:

```yaml
algorithm:
  name: binary-search
  version: 2.0.0
  previous_versions:
    - version: 1.0.0
      changelog: "Initial implementation"
    - version: 1.1.0
      changelog: "Added bounds checking for empty arrays"
  changelog: "Switched to iterative approach for better stack performance"
  deprecated: false
```

See the [versioning specification](docs/specs/versioning.md) for full details.

## Custom Primitive Types

Extend the language by defining your own algorithmic primitives:

```yaml
types:
  custom:
    - name: weighted-graph
      base: map
      properties:
        nodes: array
        edges:
          type: array
          element:
            type: map
            properties:
              from: int
              to: int
              weight: float
      constraints:
        - no_self_loops: true
        - non_negative_weights: true
```

See the [types specification](docs/specs/types.md) for full details.

## Project Structure

```
algo-lang/
├── README.md
├── ROADMAP.md
├── docs/
│   └── specs/
│       ├── language.md
│       ├── versioning.md
│       └── types.md
├── examples/
│   ├── binary-search.algo.yaml
│   ├── bubble-sort.algo.yaml
│   └── dijkstra.algo.yaml
└── schema/
    └── algo-lang.schema.yaml
```

## Documentation

- [Language Specification](docs/specs/language.md)
- [Versioning Specification](docs/specs/versioning.md)
- [Type System Specification](docs/specs/types.md)
- [Roadmap](ROADMAP.md)

## License

MIT