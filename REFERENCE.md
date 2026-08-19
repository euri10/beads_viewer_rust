# BVR Extended Reference

Everything here is Rust-only — none of it exists in `bv` (the Go original).
All commands respect `--robot-*` conventions: JSON to stdout, no TUI.

## Economics & Delivery

```bash
# Burn rate, cost-to-complete, cost-of-delay. Needs an overlay:
bvr --robot-economics --economics-overlay overlay.json
# overlay.json: {"hourly_rate": f64, "hours_per_day": f64, "budget_envelope": f64?,
#                "throughput_window_days": u32?, "currency": String?}
# or export BVR_ECONOMICS_OVERLAY=/path/to/overlay.json

bvr --robot-delivery   # Flow mix, urgency profile, milestone pressure — no overlay needed
```

## Drift & Baselines

```bash
bvr --save-baseline mybaseline
bvr --baseline-info                # When it was saved, description, stats
bvr --robot-drift                  # Machine-readable diff vs saved baseline
bvr --check-drift                  # Human-readable diff vs saved baseline
```

## Search

```bash
bvr --robot-search --search "auth timeout" --search-limit 10
bvr --search-mode <mode> --search-preset <preset> --search-weights <weights>
```

## Capacity & Forecast

```bash
bvr --robot-capacity --agents 3 --capacity-label backend   # Simulate throughput
bvr --robot-forecast <id|all> --forecast-agents 2 --forecast-label backend --forecast-sprint <id>
```

## Causality & Impact

```bash
bvr --robot-related <bead>              # Related work (--related-min-relevance, --related-max-results)
bvr --robot-blocker-chain <bead>        # Upstream blocker chain
bvr --robot-impact-network <bead> --network-depth 2   # Causal impact network
bvr --robot-causality <bead>            # Causality chain for a bead
```

## File ↔ Bead Correlation (git history evidence)

```bash
bvr --robot-orphans --orphans-min-score 30      # Repo files not covered by bead history
bvr --robot-file-beads <path> --file-beads-limit 20   # Beads related to a file
bvr --robot-file-hotspots --hotspots-limit 10   # Hotspot files ranked by history evidence
bvr --robot-impact <paths>                      # Issue impact of changed files
bvr --robot-file-relations <path> --relations-threshold 0.5 --relations-limit 10

# Review/confirm/reject a specific sha:bead correlation
bvr --robot-explain-correlation <sha:bead>
bvr --robot-confirm-correlation <sha:bead> --correlation-by <who> --correlation-reason <why>
bvr --robot-reject-correlation <sha:bead> --correlation-by <who> --correlation-reason <why>
bvr --robot-correlation-stats                   # Stored correlation feedback stats
```

## Sprints

```bash
bvr --robot-sprint-list
bvr --robot-sprint-show <sprint-id>
bvr --robot-burndown <sprint-id|current>
```

## Recommendation Feedback (tunes future scoring)

```bash
bvr --feedback-accept <rec-id>    # Positive signal
bvr --feedback-ignore <rec-id>    # Negative signal
bvr --feedback-show               # Feedback statistics
bvr --feedback-reset              # Clear all recorded feedback
```

## Emitting Actionable Scripts & Briefs

```bash
bvr --emit-script --script-limit 5 --script-format bash|fish|zsh   # Shell script for top recs
bvr --priority-brief brief.md                    # Markdown priority brief
bvr --agent-brief ./briefs/                       # Agent brief bundle in a directory
```

## Static Pages Export (human dashboards)

```bash
bvr --export-pages ./site --pages-title "Q3 Triage" --pages-include-closed=true
bvr --preview-pages ./site                        # Local server + live reload
bvr --watch-export --export-pages ./site          # Auto-regenerate on beads changes
bvr --export-md report.md                         # Markdown issue report
```

## AGENTS.md Blurb Management

```bash
bvr --agents-check                # Is the beads workflow blurb present/current?
bvr --agents-add                  # Add it (creates AGENTS.md if needed)
bvr --agents-update                # Update to current version
bvr --agents-remove                # Remove it
bvr --agents-dry-run --agents-add  # Preview without writing
```

## TUI (for Humans)

```bash
bvr --view main|board|insights|graph|history|actionable|attention|tree|labels|flow|timediff|sprint
bvr --list-filter all|open|in-progress|blocked|closed|ready
bvr --debug-render insights|board|history|main|graph --debug-width 180 --debug-height 50   # Non-interactive render, for screenshots/CI
```

## Performance & Diagnostics

```bash
bvr --robot-metrics                # Timing, cache, memory
bvr --profile-startup --profile-json
bvr --stats                        # Format token estimates on stderr
bvr --no-cache                     # Bypass disk cache for one run
bvr --force-full-analysis          # Compute all metrics regardless of graph size
bvr --check-update                 # Is a newer bvr available?
```

## jq Quick Reference

```bash
bvr --robot-triage | jq '.triage.quick_ref'
bvr --robot-triage | jq '.triage.recommendations[0]'
bvr --robot-insights | jq '.status'
bvr --robot-insights | jq '.Cycles'
bvr --robot-label-health | jq '.results.labels[] | select(.health_level == "critical")'
bvr --robot-recipes | jq '.recipes[].name'
```
