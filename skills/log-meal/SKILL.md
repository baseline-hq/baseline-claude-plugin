---
name: log-meal
description: Log food to Baseline. Use when the user says what they ate or drank, shares a meal photo or label, or asks to add something to their food diary.
---

To log a meal in Baseline:

1. Work out each item and its amount. Ask for the amount only when it changes the result a lot.
2. Find each item with `foods_search`. If nothing fits, use `foods_ai_search`. Never invent macros.
3. Show the items, amounts and macros in one short list and ask the user to confirm.
4. Log each confirmed item with `food_log_add_item` (or `food_log_add_new_item` for a food that is not in the catalog), using the meal slot the user named or the one that fits the time of day.
5. Reply with what was logged and what is left for the day from `nutrition_targets_get` and `food_log_list`. Keep it to two lines.
