# parent

> Reparents one or more nodes (one-liner style).

This modifier provides a quick way to reparent one or more nodes to a new parent node. It supports both shorthand and expanded YAML syntax.

## Example

**Inline syntax**
```yml
[mod]
parent: clothes::roots.0 spine::skin.7
```

**Expanded syntax**
```yml
[mod]
parent:
  - hair::roots.0
  - skull::skin.0
```
