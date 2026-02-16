# Type System Specification

## Version: 1.0.0

## Overview

algo-lang provides a flexible type system with built-in primitives and the ability to define custom algorithmic primitives. This document specifies all supported types and how to create new ones.

---

## Built-in Primitive Types

| Type | Description | Example Values |
|------|-------------|----------------|
| `int` | Integer numbers | `0`, `42`, `-7` |
| `float` | Floating-point numbers | `3.14`, `-0.5`, `1.0` |
| `string` | Text strings | `"hello"`, `"algo-lang"` |
| `bool` | Boolean values | `true`, `false` |
| `array` | Ordered collection | `[1, 2, 3]`, `[]` |
| `map` | Key-value pairs | `{key: "value", count: 5}` |
| `null` | Null / no value | `null` |

---

## Type Annotations

Types are used in `inputs`, `outputs`, and `types` blocks:

```yaml
inputs:
  - name: count
    type: int
  - name: label
    type: string
  - name: scores
    type: array
  - name: config
    type: map
```

---

## Parameterized Types

Array and map types can be parameterized to specify element types:

```yaml
inputs:
  - name: numbers
    type: array
    element_type: int

  - name: lookup
    type: map
    key_type: string
    value_type: float
```

---

## Custom Primitive Types

You can define new algorithmic primitives in the `types.custom` block. Custom types extend built-in primitives with additional properties and constraints.

### Syntax

```yaml
types:
  custom:
    - name: <type-name>
      base: <built-in-type>
      description: <string>
      properties: <map>
      constraints: <array>
```

### Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | ✅ | Unique type name (kebab-case) |
| `base` | string | ✅ | Built-in type this extends |
| `description` | string | ❌ | Human-readable description |
| `properties` | map | ❌ | Named sub-fields (for map-based types) |
| `constraints` | array | ❌ | Validation rules |

---

## Constraints

Constraints define validation rules for custom types:

### Array Constraints

| Constraint | Description | Example |
|------------|-------------|---------|
| `element_type` | Type of elements | `element_type: int` |
| `min_length` | Minimum array length | `min_length: 1` |
| `max_length` | Maximum array length | `max_length: 100` |
| `unique` | All elements must be unique | `unique: true` |
| `ordered` | Elements must be sorted | `ordered: ascending` or `ordered: descending` |
| `non_empty` | Array cannot be empty | `non_empty: true` |

### Numeric Constraints

| Constraint | Description | Example |
|------------|-------------|---------|
| `min` | Minimum value | `min: 0` |
| `max` | Maximum value | `max: 255` |
| `positive` | Must be positive | `positive: true` |
| `non_negative` | Must be >= 0 | `non_negative: true` |
| `even` | Must be even | `even: true` |
| `odd` | Must be odd | `odd: true` |

### String Constraints

| Constraint | Description | Example |
|------------|-------------|---------|
| `min_length` | Minimum string length | `min_length: 1` |
| `max_length` | Maximum string length | `max_length: 256` |
| `pattern` | Regex pattern | `pattern: "^[a-z]+$"` |
| `non_empty` | Cannot be empty string | `non_empty: true` |

### Map / Object Constraints

| Constraint | Description | Example |
|------------|-------------|---------|
| `required_keys` | Keys that must exist | `required_keys: [id, name]` |
| `no_extra_keys` | No keys beyond defined properties | `no_extra_keys: true` |

### Custom Constraints

| Constraint | Description | Example |
|------------|-------------|---------|
| `no_self_loops` | Graph: no edges from node to itself | `no_self_loops: true` |
| `non_negative_weights` | Graph: all weights >= 0 | `non_negative_weights: true` |
| `connected` | Graph: all nodes reachable | `connected: true` |
| `acyclic` | Graph: no cycles | `acyclic: true` |

---

## Examples

### Sorted Array

```yaml
types:
  custom:
    - name: sorted-array
      base: array
      description: "An array sorted in ascending order"
      constraints:
        - element_type: int
        - ordered: ascending
        - non_empty: true
```

### Positive Integer

```yaml
types:
  custom:
    - name: positive-int
      base: int
      description: "An integer greater than zero"
      constraints:
        - positive: true
```

### Binary Tree Node

```yaml
types:
  custom:
    - name: tree-node
      base: map
      description: "A node in a binary tree"
      properties:
        value: int
        left:
          type: tree-node
          nullable: true
        right:
          type: tree-node
          nullable: true
```

### Weighted Graph

```yaml
types:
  custom:
    - name: weighted-graph
      base: map
      description: "A graph with weighted edges"
      properties:
        nodes:
          type: array
          element_type: int
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

### Bounded Matrix

```yaml
types:
  custom:
    - name: matrix
      base: array
      description: "A 2D matrix of numbers"
      constraints:
        - element_type: array
        - min_length: 1
      properties:
        rows: int
        cols: int
```

---

## Using Custom Types

Once defined, custom types can be used anywhere a built-in type would be:

```yaml
types:
  custom:
    - name: sorted-array
      base: array
      constraints:
        - ordered: ascending
        - element_type: int

inputs:
  - name: data
    type: sorted-array
    description: "A sorted array to search"

outputs:
  - name: result
    type: int
```

---

## Type Compatibility

| From Type | Compatible With |
|-----------|----------------|
| Custom type | Its base type |
| `int` | `float` (implicit widening) |
| Any type | `null` (if `nullable: true`) |
| Constrained type | Less constrained version of same base |

Custom types are compatible with their base types. A `sorted-array` can be passed wherever an `array` is expected, but not vice versa.