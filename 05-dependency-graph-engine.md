description: Graph-based API dependency resolution engine.

---

# Node Model

Node:
  endpoint
  method
  requestSchema
  responseSchema
  emits[]
  consumes[]

---

# Edge Rule

Create edge when:

NodeA.emits ∩ NodeB.consumes ≠ ∅

---

# Detection Rules

Detect emitters:
- id
- uuid
- token
- referenceId
- orderId
- userId

Detect consumers:
- Path params
- Request body references
- $ref schema reuse

---

# Execution Order

- Build directed graph.
- Perform topological sort.
- If cycle detected:
  - Flag as advanced flow.
  - Require seed data.

---

# Flow Types

- CRUD lifecycle
- Parent-child relationship
- Auth-token chain
- Multi-step transactions
