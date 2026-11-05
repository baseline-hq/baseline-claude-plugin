---
name: plan-workout
description: Plan or adjust a workout routine in Baseline. Use when the user asks for a workout, a training plan, or wants to change a routine.
---

To plan a workout:

1. Read the user's goals with `goals_list`, recent training with `workouts_list`, and existing routines with `routines_list`.
2. Pick exercises with `exercises_search` or `exercises_find_by_muscle_group`. Use `exercise_get_progression` to choose weights and reps from what the user lifted last time.
3. Show the plan as a short list (exercise, sets x reps, weight) and ask before saving.
4. Save it with `routines_create`, or change an existing routine with `routines_update`, `routines_add_exercise` and `routines_add_set`.
5. Confirm before any delete.
