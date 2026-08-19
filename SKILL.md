---
name: bvr
description: "Beads Viewer Rust (bvr) - graph-aware triage engine for Beads projects. Rust rewrite and superset of bv: same graph metrics (PageRank, betweenness, critical path, cycles) plus economics, delivery posture, drift/baseline, search, capacity simulation, causality/impact analysis, and a token-optimized `toon` output format. Use --robot-* flags for AI agents."
---

# BVR - Beads Viewer (Rust)

A graph-aware triage engine for Beads projects (`.beads/beads.jsonl`, with
`issues.jsonl`/`beads.base.jsonl` compat). This is the Rust rewrite of `bv`
(github.com/Dicklesworthstone/beads_viewer) — same core graph metrics and
robot-flag contract, plus a substantial set of analytics `bv` doesn't have.
Human TUI for browsing; robot flags for AI agents.

## CRITICAL: Robot Mode for Agents

**Never run bare `bvr`**. It launches an interactive TUI that blocks your session.

Always use `--robot-*` flags:

```bash
bvr --robot-triage        # THE MEGA-COMMAND: start here
bvr --robot-next          # Minimal: just the single top pick
bvr --robot-overview      # Compact orientation snapshot for fast agent loops
bvr --robot-plan          # Parallel execution tracks
bvr --robot-insights      # Full graph metrics
```

## Self-Documenting: Don't Guess Output Shapes

`bvr` ships its own machine-readable docs and schemas — prefer these over
memorized JSON shapes, since they reflect the exact installed version:

```bash
bvr --robot-docs all                       # guide|commands|examples|env|exit-codes|all
bvr --robot-schema                         # JSON Schema for every robot command
bvr --robot-schema --schema-command triage # Schema for one command
```

## Output Format: json vs toon

`--format toon` emits a token-optimized notation that cuts output size
~30-50% vs `json` (env: `BV_OUTPUT_FORMAT`, `TOON_DEFAULT_FORMAT`). Default
stays `json` for jq-ability; reach for `toon` when piping large payloads
(e.g. `--robot-insights`, `--robot-history`) straight into a prompt.

## The 9 Graph Metrics

| Metric | What It Measures | Key Insight |
|--------|------------------|-------------|
| **PageRank** | Recursive dependency importance | Foundational blockers |
| **Betweenness** | Shortest-path traffic | Bottlenecks and bridges |
| **HITS** | Hub/Authority duality | Epics vs utilities |
| **Critical Path** | Longest dependency chain | Keystones with zero slack |
| **Eigenvector** | Influence via neighbors | Strategic dependencies |
| **Degree** | Direct connection counts | Immediate blockers/blocked |
| **Density** | Edge-to-node ratio | Project coupling health |
| **Cycles** | Circular dependencies | Structural errors (must fix!) |
| **Topo Sort** | Valid execution order | Work queue foundation |

Always check the `status` field in output (`computed|approx|timeout|skipped`).

## Core Robot Commands

```bash
# Triage & planning
bvr --robot-triage              # Full triage: recommendations, quick_wins, blockers_to_clear
bvr --robot-next                # Single top pick
bvr --robot-plan                # Parallel execution tracks with unblocks lists
bvr --robot-priority            # Priority misalignment detection
bvr --robot-triage-by-track     # Group by parallel work streams
bvr --robot-triage-by-label     # Group by domain

# Graph analysis
bvr --robot-insights            # Full metrics: PageRank, betweenness, HITS, cycles
bvr --robot-label-health        # Per-label health, velocity, staleness
bvr --robot-label-flow          # Cross-label dependency flow matrix
bvr --robot-label-attention     # Attention-ranked labels

# History & changes
bvr --robot-history             # Bead-to-commit correlations
bvr --robot-diff --diff-since <ref>
bvr --robot-alerts              # Stale issues, blocking cascades
bvr --robot-suggest             # Hygiene: duplicates, missing deps, cycle breaks

# Graph export
bvr --robot-graph --graph-format=dot|mermaid|json
bvr --export-graph report.html  # Self-contained interactive HTML
```

## Scoping, Filtering & Recipes

```bash
bvr --robot-plan --label backend
bvr --robot-insights --as-of HEAD~30
bvr --recipe actionable --robot-plan     # Ready to work
bvr --recipe high-impact --robot-triage  # Top PageRank
bvr --robot-recipes                      # List all recipes
bvr --weight-preset quick-wins           # Scoring preset (default|graph-heavy|priority-first|quick-wins|risk-averse)
```

## Rust-Only: Extended Analytics

`bvr` adds capabilities `bv` never had — economics projections, delivery
posture, drift detection, full-text search, capacity simulation, and
causal/impact analysis. See [REFERENCE.md](REFERENCE.md) for the full
command list, flags, and examples.

## Agent Workflow Pattern

```bash
TRIAGE=$(bvr --robot-triage)
NEXT_TASK=$(echo "$TRIAGE" | jq -r '.triage.recommendations[0].id')

CYCLES=$(bvr --robot-insights | jq '.Cycles')
[ "$CYCLES" != "[]" ] && echo "Fix cycles first: $CYCLES"

bd claim "$NEXT_TASK"   # or: br claim "$NEXT_TASK"
# ... work ...
bd close "$NEXT_TASK"   # or: br close "$NEXT_TASK"
```

## Common Pitfalls

| Issue | Fix |
|-------|-----|
| TUI blocks agent | Use `--robot-*` flags only |
| Stale metrics | Check `status` field, results cached by `data_hash` |
| Missing cycles | Run `--robot-insights`, check `.cycles` |
| Wrong recommendations | Use `--recipe actionable` to filter to ready work |
| Guessing JSON shape | Run `--robot-schema` / `--robot-docs` instead |
