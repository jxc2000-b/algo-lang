# Algorithmic Versioning Specification

## Version: 1.0.0

## Overview

Algorithmic versioning is a core feature of algo-lang that allows you to track how algorithms evolve over time. It uses semantic versioning principles adapted specifically for algorithm definitions.

---

## Semantic Versioning for Algorithms

algo-lang follows a **MAJOR.MINOR.PATCH** versioning scheme:

| Component | When to Increment | Example |
|-----------|-------------------|---------|
| **MAJOR** | Breaking changes to inputs, outputs, or fundamental logic | `1.0.0` → `2.0.0` |
| **MINOR** | New optional inputs, performance improvements, added edge case handling | `1.0.0` → `1.1.0` |
| **PATCH** | Bug fixes, documentation updates, comment changes | `1.0.0` → `1.0.1` |

### Breaking Changes (MAJOR)

The following are considered breaking changes:
- Removing or renaming an input or output parameter
- Changing the type of an input or output
- Fundamentally altering the algorithm's approach (e.g., recursive → iterative with different guarantees)
- Changing the algorithm's output for the same input

### Non-Breaking Changes (MINOR)

- Adding a new optional input with a default value
- Improving time or space complexity
- Adding handling for edge cases that previously caused errors
- Adding new custom types used internally

### Patches (PATCH)

- Fixing incorrect step logic that didn't match the description
- Updating comments or descriptions
- Fixing metadata (tags, author, etc.)

---

## Version Metadata Fields

### `version` (Required)

The current version of the algorithm:

```yaml
algorithm:
  name: quick-sort
  version: 2.1.0
```

### `previous_versions` (Optional)

An array tracking the history of the algorithm:

```yaml
algorithm:
  name: quick-sort
  version: 2.1.0
  previous_versions:
    - version: 1.0.0
      date: "2026-01-15"
      changelog: "Initial implementation with Lomuto partition"
    - version: 1.1.0
      date: "2026-01-20"
      changelog: "Added randomized pivot selection"
    - version: 2.0.0
      date: "2026-02-01"
      changelog: "Switched to Hoare partition scheme, changed partition output format"
```

Each entry in `previous_versions` supports:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `version` | string | ✅ | The version number |
| `date` | string | ❌ | ISO 8601 date of the release |
| `changelog` | string | ✅ | Description of changes |
| `author` | string | ❌ | Who made the changes |
| `deprecated` | bool | ❌ | Whether this version was deprecated |

### `changelog` (Optional)

A description of what changed in the current version:

```yaml
algorithm:
  name: quick-sort
  version: 2.1.0
  changelog: "Added median-of-three pivot selection for better worst-case performance"
```

### `deprecated` (Optional)

Marks an algorithm version as deprecated:

```yaml
algorithm:
  name: bubble-sort
  version: 1.0.0
  deprecated: true
  deprecation_message: "Use merge-sort or quick-sort for better performance"
  successor:
    algorithm: merge-sort
    version: ">=1.0.0"
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `deprecated` | bool | ❌ | Whether this version is deprecated |
| `deprecation_message` | string | ❌ | Why it's deprecated |
| `successor` | object | ❌ | Recommended replacement algorithm |

---

## Version Constraints

When referencing other algorithms (e.g., in `call` steps), you can specify version constraints:

| Constraint | Meaning | Example |
|------------|---------|---------|
| Exact | Must match exactly | "1.2.3" |
| Range | Minimum version | ">=1.0.0" |
| Compatible | Same major version | "^1.0.0" (matches `1.x.x`) |
| Approximate | Same major.minor | "~1.2.0" (matches `1.2.x`) |
| Latest | Always use latest | "latest" |

### Example

```yaml
steps:
  - call:
      algorithm: binary-search
      version: "^1.0.0"    # Any 1.x.x version
      inputs:
        arr: sorted_data
        target: value
      output: result
```

---

## Full Example

```yaml
algorithm:
  name: binary-search
  version: 2.0.0
  description: "Iterative binary search on a sorted array"
  author: "jxc2000-b"
  tags: [search, divide-and-conquer]
  complexity:
    time: O(log n)
    space: O(1)
  changelog: "Switched from recursive to iterative approach for O(1) space"
  deprecated: false
  previous_versions:
    - version: 1.0.0
      date: "2026-01-10"
      changelog: "Initial recursive implementation"
      author: "jxc2000-b"
    - version: 1.1.0
      date: "2026-01-25"
      changelog: "Added bounds checking for empty arrays"
      author: "jxc2000-b"
    - version: 1.2.0
      date: "2026-02-05"
      changelog: "Added support for custom comparators via optional input"
      author: "jxc2000-b"
```