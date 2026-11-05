---
name: weekly-review
description: Review the user's week in Baseline. Use when the user asks how their week went, wants a weekly summary, or asks about their check-in, weight trend or targets.
---

To review the week:

1. Read the latest week with `health_weekly_latest`, compare it with the week before using `health_weekly_compare`, and read `check_ins_get_latest_weekly` and `body_weight_get_trend`.
2. Summarize in three parts: food (average calories and protein against targets), training (sessions and volume), and body (weight trend, sleep and steps).
3. Name one thing that went well and one thing to change next week. Use the user's own numbers.
4. If the check-in proposes new targets, show the proposal and ask before calling `check_ins_update_weekly_decision`.

Don't diagnose. Suggest a clinician for medical concerns.
