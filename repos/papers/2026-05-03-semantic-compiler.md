# Semantic Compiler: From Intent to Verified Behavior

> "The compiler's job is to make the agent's output provably match the spec. Not probably. Not usually. Provably."

---

## The Problem with Prompting

Prompts are lossy. You write a natural language instruction, the LLM interprets it, produces output, and you hope — hope — that the output matches what you actually meant.

This is the fundamental problem: natural language is not a specification language. Natural language evolved to convey intent between humans who share context, culture, and common sense. LLMs share neither your context nor your culture. They share your tokens.

The Semantic Compiler is an attempt to close this gap. Our core thesis: **natural language specs → semantic AST → compiled agent behavior → verified against spec**. The compiler makes the agent's output provably match the spec.

---

## Architecture: Three Planes of Compilation

### Plane 1: Intent Parsing

The input is natural language. The output of Plane 1 is a Semantic AST — a typed, structured representation of what the user actually wants.

A Semantic AST has five components:

**Entities** — the objects in the request: files, functions, users, systems, APIs, databases. Each entity has a type and a set of known properties.

**Relations** — how entities relate to each other: depends-on, contains, implements, authenticates-via, stores-in. Relations form the structure of the problem space.

**Constraints** — hard requirements (must-have) and soft preferences (should-have). Hard constraints are exit conditions — if violated, the compilation fails. Soft constraints are optimization targets.

**Verbs** — the actions to perform: create, modify, delete, verify, deploy, rollback. Each verb has a signature: what it takes in, what it produces, what it requires.

**Context** — what the agent already knows that it should not re-derive. Context is inherited from PLATO tiles written by prior agents. The compiler reuses, not re-learns.

Example — a user says: *"Write a REST API that handles user authentication with JWTs and stores sessions in Redis"*

```
Semantic AST:
  entities: [User, REST_API, Authentication, JWT, Session, Redis, Database]
  relations:
    REST_API implements Authentication
    Authentication uses JWT
    Session stored_in Redis
    User authenticates_via Authentication
  constraints:
    hard: [JWT_required, Redis_session_ttl_less_than_3600, CORS_enabled, HTTPS_only]
    soft: [idempotent_endpoints, rate_limiting_per_ip, structured_logging]
  verbs: [create, authenticate, validate, store, retrieve, invalidate]
  context:
    protocol: HTTP/2
    default_port: 8080
    auth_scheme: Bearer
    redis_protocol: RESP
```

This AST is unambiguous. Two compilers reading the same AST produce the same constraint graph.

### Plane 2: Constraint Compilation

The Semantic AST is compiled into a **Constraint Graph** — a directed acyclic graph (DAG) where nodes are actions and edges are dependencies.

Each node carries three things:
- **Preconditions**: what must be true before this action executes
- **Postconditions**: what must be true after this action completes
- **Invariant**: what must remain true throughout execution

The constraint graph is the "compiled" form of the intent. It's deterministic, machine-checkable, and can be verified against the original Semantic AST.

Crucially, the constraint graph is **persistent**. Once compiled, it can be:
- Re-executed against new inputs
- Shared across agents (PLATO tile)
- Version-controlled
- Audited

### Plane 3: Execution + Verification

The constraint graph is executed by the agent. At each step:

1. The agent proposes an action
2. The verifier checks: does this action satisfy the next node's preconditions?
3. After execution: does the postcondition hold?
4. If verification fails: rollback, retry, or escalate to human

This is the key innovation: **verification is compile-time, not runtime**. The agent doesn't try things and hope they work. It proves each action will work before executing it.

---

## PLATO Connection: The 5-Atom Chain

Every meaningful tile in PLATO has a 5-atom verification chain:

1. **Claim** — what the tile says (the output of the action)
2. **Evidence** — the data or reasoning behind the claim
3. **Inference** — how the evidence supports the claim
4. **Scope** — where the claim applies (and where it doesn't)
5. **Confidence** — how certain the claim is (calibrated probability, not vibes)

The Semantic Compiler's verification loop is the execution-time version of this chain. Each constraint node in the graph is a tile. Each verification pass checks one of the 5 atoms.

When an agent writes a tile during compilation, it's not just logging — it's contributing to the verification chain of the entire fleet. Future agents can read the tile and trust it (or verify it themselves) without re-running the computation.

---

## Why This Beats Raw Prompting

| Prompting | Semantic Compiler |
|-----------|------------------|
| Natural language (ambiguous) | Typed AST (precise) |
| Output checked by human | Output verified by proof |
| Hallucination structurally possible | Hallucination structurally impossible |
| Context window is the limit | Constraint graph is unbounded |
| Every run starts fresh | Compiled form persists and reuses |
| LLM interprets intent | Compiler enforces intent |
| One-off | Composable |

The Semantic Compiler doesn't eliminate the LLM. It confines the LLM to the right task: generating candidates. Verification is deterministic. Interpretation is not.

---

## The Boat Analogy

You can shout instructions to a boat crew in plain English: "Hold her steady on that heading, watch for the rocks near the point, and let me know when we're in safe water."

Sometimes they get it right. Sometimes the rocks weren't on the chart and nobody knew. Sometimes "steady" meant different things to different people. Sometimes the tide was running harder than expected and the boat broached.

Or you can give them a chart, a bearing, a set of waypoints, and a rule: "If you're within 2 cables of any marked hazard, sound three blasts and come about immediately. If the GPS shows you're outside the channel, reduce to steerageway only."

The boat doesn't need to interpret your intent. It follows the constraint.

The Semantic Compiler turns shouting into chartwork. The crew still needs skill — but the skill is applied correctly, not guessed at.

---

## FLUX Bytecode: The Compile Target

The constraint graph compiles to FLUX bytecode — a binary format that can be executed by any FLUX runtime without an LLM in the loop.

FLUX bytecode nodes include:
- `IDENTITY` — asserts entity existence
- `CONSTRAINT` — checks a precondition or postcondition
- `BRANCH` — conditional execution based on constraint satisfaction
- `COMPOSE` — combines multiple constraints
- `TILE_WRITE` — persists a verified claim to PLATO
- `VERIFY_TILE` — checks a PLATO tile's 5-atom chain

The semantic compiler is in `flux-compiler/`. The constraint graph to FLUX bytecode compiler is in `flux-compiler/src/codegen.rs`. It's ~800 lines of Rust. It works for a subset of constraints — we're expanding coverage.

---

## Open Questions

**1. How do we handle intent that genuinely can't be formalized?**

Some things are irreducibly ambiguous in natural language. The compiler should fail gracefully: flag the ambiguous parts, ask for clarification, and continue only when the constraint graph is complete. We don't want to force-fit everything into a schema and lose the expressiveness of natural language in the process.

**2. Does the Semantic AST have a standard schema?**

We're working toward one. The schema must be expressive enough to capture any intent, constrained enough to be machine-verifiable. We're drawing from DMN (Decision Model and Notation), ECM (Event Condition Action), and type theory. This is a hard design problem and the subject of active research.

**3. Can agents self-verify without LLM involvement?**

Currently, verification still uses an LLM to check postconditions. We're exploring formal methods for a subset of constraints — type checking, protocol compliance, resource bounds — where no LLM is needed. The goal: for every FLUX bytecode node, there's a proof that either succeeds or fails without invoking an LLM. LLM involvement is the slow path, not the happy path.

**4. What does "compiled agent behavior" look like in practice?**

We have a working prototype. The constraint graph is serialized as FLUX bytecode. The verifier is a 200-line Rust module. It handles identity constraints and composition correctly. End-to-end verification of a multi-step plan is the next milestone.

---

## Conclusion

The Semantic Compiler is not a finished system. It's an architecture. The key insight is that **verification and execution are the same act** — not separate steps bolted together after the fact.

When you compile intent into a constraint graph, you make the agent's behavior not just understandable, but provable. The agent doesn't hope it does the right thing. The constraint graph proves the right thing is the only option.

The ocean doesn't flow in infinite precision. Neither should agent behavior. The Semantic Compiler is the chartwork that keeps us on course.
