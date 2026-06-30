# Threshold Analysis - Trail Guide Agent

## Overview

This analysis compares pass rates at different score thresholds to recommend
the optimal production threshold for each evaluator.

## Pass Rate Comparison by Threshold

| Evaluator | Threshold >= 3.0 | Threshold >= 3.5 | Threshold >= 4.0 | Threshold >= 4.5 |
|-----------|-----------------|-----------------|-----------------|-----------------|
| Intent Resolution | 27.0% | 18.0% | 11.2% | 4.5% |
| Relevance | 84.3% | 71.9% | 56.2% | 33.7% |
| Groundedness | 65.2% | 52.8% | 38.2% | 21.3% |

## Analysis

### Impact of Stricter Thresholds

- **Threshold 3.0 (Default):** Baseline pass rates. Relevance performs well (84.3%), but Intent Resolution is already low (27.0%).
- **Threshold 4.0 (Strict):** All metrics drop significantly. Only 56.2% of responses pass Relevance, 38.2% pass Groundedness, and just 11.2% pass Intent Resolution.
- **Threshold 4.5 (Very Strict):** Less than a third of responses pass any evaluator. Not viable for production without major agent improvements.

### Key Observations

1. **Relevance is most robust:** Even at threshold 4.0, over half of responses pass. This indicates the agent generally stays on-topic.
2. **Intent Resolution needs work first:** Already failing at 3.0 threshold; raising it further makes the metric impractical as a gate.
3. **Groundedness degrades linearly:** Each 0.5 increase in threshold drops pass rate by ~13%, suggesting consistent but modest over-elaboration.

## Production Threshold Recommendation

| Use Case | Recommended Threshold | Justification |
|----------|----------------------|---------------|
| Development/Testing | 3.0 | Catches major regressions without blocking iteration |
| Staging/Pre-prod | 3.5 | Balances quality with deployment velocity |
| Production (general) | 3.5 | Best trade-off: Relevance 71.9%, Groundedness 52.8% |
| Production (safety-critical) | 4.0 | Only for use cases where accuracy > coverage |

### Final Recommendation

**Use threshold 3.5 for production quality gates** with the following rationale:
- Relevance pass rate (71.9%) remains acceptable for most use cases
- Groundedness (52.8%) identifies responses needing improvement without rejecting the majority
- Intent Resolution should be addressed through prompt engineering before being used as a hard gate
- Configure CI/CD to warn (not block) on Intent Resolution < 3.5, and block on Relevance or Groundedness < 3.5
