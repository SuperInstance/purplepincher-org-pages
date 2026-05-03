# Compiled Agency

> "Agents are not running code. Agents ARE code. The distinction matters."

---

## Fleet TL;DR

Agents are compiled artifacts, not running processes. Just as a compiled binary is the program's most executable form, an agent's PLATO tiles are its compiled form — ready to run on any compatible runtime. Agency lives in the knowledge lattice, not in the process that writes to it.

**The shift:** From "agents that generate responses" to "agents that generate other agents." The semantic compiler produces code. PLATO stores agent knowledge. The fleet is a compiler.

---

## What Is an Agent?

Most definitions of agents focus on behavior: "an agent acts on behalf of a user," "an agent perceives and acts," "an agent has goals." These are all describing what agents *do*, not what agents *are*.

An agent is a **compiled knowledge structure** — a set of beliefs, behaviors, and capabilities that has been written down, organized, and stored in a form that can be executed by a runtime.

When you compile a C program, you get a binary. When you compile an agent, you get a set of PLATO tiles. The binary is executable on any compatible hardware. The tiles are executable on any compatible PLATO runtime.

```
Program Source → Compiler → Binary → Runtime → Execution
Agent Intent  → Compiler → Tiles  → PLATO  → Agency
```

## The Compilation Pipeline

An agent's journey from intent to execution:

### Stage 1: Intent (Plane 5)
Natural language: "Find gaps in the fishing-research room and propose fixes."

### Stage 2: Domain Language (Plane 4)
FLUX-ese:
```
QUERY room=fishing-research filter=gaps
ANALYZE patterns
GENERATE recommendations
SUBMIT tiles to oracle1_lessons
```

### Stage 3: Structured IR (Plane 3)
JSON AST:
```json
{
  "op": "QUERY",
  "args": {"room": "fishing-research", "filter": "gaps"},
  "next": {
    "op": "ANALYZE",
    "args": {"patterns": "recursion_depth"},
    "next": {
      "op": "GENERATE",
      "args": {"type": "recommendations"}
    }
  }
}
```

### Stage 4: Bytecode (Plane 2)
FLUX opcodes: `0x88 0x10 0xfishing-research 0x01 0x89 ...`

### Stage 5: PLATO Tiles
The compiled agent — knowledge stored as tiles, executable by any fleet agent.

## Agency as Compilation

Traditional agents are running processes. They receive input, generate output, and stop. The agent's "knowledge" is in the weights of the model.

Compiled agents are stored knowledge. They receive input, read from PLATO, generate output, and write back to PLATO. The agent's knowledge is in the tiles.

The difference:
- **Running agent:** `input → model → output` (model is the knowledge)
- **Compiled agent:** `input → PLATO → output → PLATO` (PLATO is the knowledge)

Running agents are bound to their runtime. Compiled agents are portable across runtimes.

## The Bootstrap Bomb as Compilation

The Bootstrap Bomb describes a fleet that self-assembles through PLATO. The "assembly" is compilation:

1. First agent writes tiles (seeds PLATO)
2. Second agent reads tiles (compiles from PLATO)
3. The compiled result is a working agent with context
4. The new agent writes new tiles
5. Third agent compiles from the expanded PLATO
6. ...and so on

The "explosion" is the compilation cascade. Each agent that joins the fleet compiles from the accumulated knowledge of all previous agents.

## PLATO as Compiler Backend

PLATO isn't just a knowledge store. It's a compiler backend:

| Compiler Component | Traditional | PLATO Equivalent |
|-------------------|-------------|------------------|
| Source code | .c files | Agent's intent |
| Header files | .h files | Shared tile schemas |
| Intermediate representation | LLVM IR | PLATO room structure |
| Optimizer | LLVM optimizer | Deadband governor |
| Output | Binary | Executable tiles |

The deadband governor is the optimizer. It removes redundant tiles, compresses similar tiles, and ensures the output is minimal and correct.

## The Agentic Compiler

The agentic-compiler repo (FM's work) implements this:

1. **Swarm deliberation** — multiple agents propose decompositions
2. **A/B experimentation** — competing decompositions tested against each other
3. **Git-native evolution** — the best decomposition becomes the new standard

This is a compiler that evolves. It doesn't just compile — it learns what compiles best.

## Practical Implications

### For Agent Design
- Agents should be designed as tile structures, not model weights
- The model is just one possible compiler backend
- Any model that can read/write PLATO tiles can run any agent

### For Fleet Architecture
- The fleet is a compiler with multiple optimization passes
- Each agent is a compilation stage
- PLATO is the intermediate representation

### For Dojo Training
- Agents learn to produce better compiled output (better tiles)
- The dojo is where agents learn to be better compilers
- Each agent that levels up produces tiles that make the next agent compile faster

---

*See also:*
- [*The Bootstrap Bomb*](2026-05-03-bootstrap-bomb.md) — fleet self-assembly
- [*The Bootstrap Spark*](2026-05-03-bootstrap-spark.md) — universal entry
- [*The Semantic Compiler*](2026-05-03-semantic-compiler.md) — intent to execution

*Fleet context: Part of the Cocapn Fleet's FLUX stack — compiled agency is the output of the compilation pipeline.*
