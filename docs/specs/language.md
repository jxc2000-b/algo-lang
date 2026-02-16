# algo-lang Language Specification

## Version: 1.0.0

## Overview

algo-lang is a YAML-based domain-specific language for defining algorithms in a structured, human-readable format. This document defines the core language specification.

---

## File Format

- algo-lang files use the `.algo.yaml` extension
- Files must be valid YAML 1.2
- UTF-8 encoding is required

---

## Top-Level Structure

Every algo-lang file contains the following top-level keys:

```yaml
algorithm:    # Required — Algorithm metadata
types:        # Optional — Type declarations
inputs:       # Required — Input parameters
outputs:      # Required — Output parameters
steps:        # Required — Algorithm logic
```

---

## Algorithm Metadata

The `algorithm` block defines metadata about the algorithm:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | ✅ | Unique identifier (kebab-case) |
| `version` | string | ✅ | Semantic version (e.g., `1.0.0`) |
| `description` | string | ✅ | Human-readable description |
| `author` | string | ❌ | Author name or identifier |
| `tags` | array[string] | ❌ | Categorization tags |
| `complexity.time` | string | ❌ | Time complexity in Big-O notation |
| `complexity.space` | string | ❌ | Space complexity in Big-O notation |
| `previous_versions` | array | ❌ | Version history (see versioning spec) |
| `changelog` | string | ❌ | Changes in this version |
| `deprecated` | bool | ❌ | Whether this version is deprecated |

### Example

```yaml
algorithm:
  name: merge-sort
  version: 1.0.0
  description: "Divide-and-conquer sorting algorithm"
  author: "jxc2000-b"
  tags: [sorting, divide-and-conquer, stable]
  complexity:
    time: O(n log n)
    space: O(n)
```

---

## Inputs & Outputs

Inputs and outputs define the algorithm's interface.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | ✅ | Parameter name |
| `type` | string | ✅ | Type (built-in or custom) |
| `description` | string | ❌ | Human-readable description |
| `default` | any | ❌ | Default value (inputs only) |

### Example

```yaml
inputs:
  - name: arr
    type: array
    description: "The array to sort"
  - name: ascending
    type: bool
    description: "Sort order"
    default: true

outputs:
  - name: sorted_arr
    type: array
    description: "The sorted array"
```

---

## Steps

The `steps` block is an ordered list of operations that define the algorithm's logic.

### Supported Operations

#### `set` — Variable Assignment

```yaml
- set:
    variable_name: expression
```

#### `if` / `elif` / `else` — Conditional Branching

```yaml
- if:
    condition: x > 0
    then:
      - set:
          result: "positive"
- elif:
    condition: x == 0
    then:
      - set:
          result: "zero"
- else:
    - set:
        result: "negative"
```

#### `while` — While Loop

```yaml
- while:
    condition: i < length(arr)
    do:
      - set:
          i: i + 1
```

#### `for` — For Loop

```yaml
- for:
    variable: i
    from: 0
    to: length(arr) - 1
    do:
      - set:
          sum: sum + arr[i]
```

#### `return` — Return a Value

```yaml
- return: result
```

#### `call` — Call Another Algorithm

```yaml
- call:
    algorithm: binary-search
    inputs:
      arr: sorted_data
      target: search_value
    output: search_result
```

---

## Expressions

Expressions are used in conditions, assignments, and return values.

### Supported Operators

| Category | Operators |
|----------|-----------|
| Arithmetic | `+`, `-`, `*`, `/`, `%` |
| Comparison | `==`, `!=`, `<`, `>`, `<=`, `>=` |
| Logical | `and`, `or`, `not` |
| Array Access | `arr[index]` |
| Map Access | `map.key` or `map["key"]` |

### Built-in Functions

| Function | Description |
|----------|-------------|
| `length(x)` | Length of array or string |
| `min(a, b)` | Minimum of two values |
| `max(a, b)` | Maximum of two values |
| `abs(x)` | Absolute value |
| `floor(x)` | Floor division |
| `ceil(x)` | Ceiling division |
| `swap(a, b)` | Swap two values |
| `append(arr, x)` | Append to array |
| `pop(arr)` | Remove last element |
| `contains(arr, x)` | Check if array contains value |

---

## Comments

YAML comments (`#`) are supported and encouraged for documentation:

```yaml
steps:
  # Initialize the search boundaries
  - set:
      low: 0
      high: length(arr) - 1

  # Main search loop
  - while:
      condition: low <= high
      do:
        - set:
            mid: (low + high) / 2  # Calculate midpoint
```