# The Semantic Compiler

> "A compiler that understands what you mean, not just what you typed."

---

## Fleet TL;DR

A compiler that decomposes natural language intent into executable code at the right abstraction level. Not all the way to bytecode — just far enough. The sweet spot is FLUX-ese (domain language), not assembly. The compiler finds the optimal plane and generates the right output for the target hardware.

**Why it matters:** Agents write in natural language. Hardware runs on bytecode. The semantic compiler bridges that gap — not by compiling everything to machine code, but by compiling to the right level for the job.

---

## The Problem with Traditional Compilers

Traditional compilers take precise syntax and produce precise output. They're exact, deterministic, and completely blind to intent.

```
C code → lexer → parser → AST → optimizer → bytecode → executable
```

The compiler doesn't know what you're trying to do. It only knows what you wrote. If you write `x = y + z`, the compiler produces `ADD X, Y, Z`. It has no opinion on whether `y` and `z` should be added at all.

This is fine for humans who know what they want. It's terrible for agents who need to figure out what to do.

## The Semantic Compiler's Approach

A semantic compiler takes intent and produces execution:

```
Intent ("navigate east 10 knots, alert if reactor overheats")
  → decomposer (find the right abstraction plane)
  → domain language (FLUX-ese)
  → optional: structured IR → bytecode → native
```

The key insight: **most intents stop at FLUX-ese (Plane 4)**. The compiler's job is to find that plane and stop there. Only when targeting specific hardware (ESP32, Jetson, bare metal) does it go deeper.

## The 6 Abstraction Planes

From FM's abstraction-planes framework:

| Plane | Name | Output | When to Stop |
|-------|------|--------|-------------|
| 5 | Intent | Natural language | Start here |
| 4 | Domain Language | FLUX-ese | **Most intents** — this is the sweet spot |
| 3 | Structured IR | JSON AST + types | Complex logic, cross-language |
| 2 | Bytecode | FLUX hex opcodes | Target specific VM |
| 1 | Native | C / Rust | Target bare metal |
| 0 | Metal | Assembly | Embedded systems only |

## How the Compiler Works

### Step 1: Intent Parsing

The compiler receives a natural language intent:
```
"navigate east 10 knots, alert if reactor overheats"
```

### Step 2: Plane Detection

The compiler determines the optimal plane based on:
- Target hardware (Jetson → native, cloud → FLUX-ese)
- Complexity (simple → FLUX-ese, complex → IR)
- Performance requirements (high-perf → native, general → FLUX-ese)

### Step 3: Decomposition

The intent decomposes to the optimal plane:

**Target: cloud/agent**
```
IF gauge.reactor > 100 THEN ALERT "reactor_overheat"
NAVIGATE bearing=90 speed=10
```

**Target: Jetson**
```rust
if readGauge(REACTOR_TEMP) > 100 {
    triggerAlert("reactor_overheat");
}
navigate(90.0, 10.0);
```

### Step 4: Output Generation

The decomposed intent generates output at the target plane:
- FLUX-ese → passes to FLUX runtime
- Native → compiles via gcc/clang/rustc
- Bytecode → passes to FLUX VM

## The Agentic Advantage

When agents use the semantic compiler:

1. **Agents write intent, not code.** They focus on what, not how.
2. **The compiler handles the how.** Target hardware, optimization, format.
3. **The output is always executable.** No syntax errors, no type mismatches.
4. **The abstraction level is correct.** Not over-engineered, not under-powered.

## The PLATO Connection

The semantic compiler writes to PLATO:
- Decomposition decisions → lessons room
- Optimal plane findings → domain room
- Failed decompositions → questions room (what went wrong)

PLATO becomes the compiler's memory. Future compilations draw from past decompositions.

## Implementation

The compiler has 4 components:

| Component | Purpose | Location |
|-----------|---------|----------|
| IntentParser | Parse natural language to structured intent | plato-tutor |
| PlaneSelector | Determine optimal abstraction plane | abstraction-planes |
| Decomposer | Decompose intent to target plane | agentic-compiler |
| CodeGen | Generate output at target plane | flux-compiler |

## Example: Navigation Intent

**Input:**
```
Navigate east for 10 nautical miles. Monitor reactor temperature. If reactor exceeds 100°C, alert the crew and change course to north.
```

**Decomposition to FLUX-ese (Plane 4):**
```
NAVIGATE bearing=90 distance=10 unit=nautical_miles
MONITOR gauge=reactor_temp threshold=100
IF gauge.reactor_temp > 100 THEN {
  ALERT "reactor_overheat" severity=high
  NAVIGATE bearing=0 speed=5
}
```

**Decomposition to native (Plane 1, Jetson target):**
```c
navigate(90.0, 10.0);  // nautical miles to km/s conversion handled
while (readGauge(REACTOR_TEMP) <= 100) {
    monitor();
}
triggerAlert("reactor_overheat");
navigate(0.0, 5.0);  // northward escape
```

## The Dojo Connection

In the greenhorn dojo, agents learn to write better intents:
- The compiler's feedback loop teaches intent refinement
- Poor intents produce poor FLUX-ese
- Good intents produce clean, executable code

The dojo and the compiler form a feedback loop: the compiler produces output, the output trains the agent to write better intents, better intents produce better output.

---

*See also:*
- [*The Bootstrap Spark*](2026-05-03-bootstrap-spark.md) — universal minimum ignition state
- [*The Bootstrap Bomb*](2026-05-03-bootstrap-bomb.md) — fleet self-assembly
- [*Abstraction Planes*](https://github.com/SuperInstance/abstraction-planes) — the 6-plane framework

*Fleet context: Part of the Cocapn Fleet's FLUX stack — semantic compilation is how intent becomes execution.*
