---
name: punishments
description: >-
  Audit a CF34 assignment for required HTML and CSS rules, detect missing
  submissions, and add punishments to landing/public/data.json. Use when the
  user asks to check, grade, or punish students for an eNN or qNN assignment.
---

# Punishments

Audit one assignment folder and update each student's `pizzaSlices` in
`landing/public/data.json`. Do not commit or push unless the user asks.

## Rules

Each failed rule adds one punishment per student:

1. all `a` tags should have `title` attribute
2. all `img` tags should have `alt` attribute
3. there should be `* {box-sizing: border-box}` present in the project
4. there should not be any other rules other than `width: xxx` and `margin: auto` on `.container` class

Count a rule only once per student, even when it has multiple violations. A
student can therefore receive at most four code punishments for one assignment.

Interpret the rules literally:

- `title=""` and `alt=""` satisfy attribute presence.
- The universal selector must declare `box-sizing: border-box`.
- A `.container` rule must contain exactly one `width` declaration and one
  `margin: auto` declaration. Any other declaration, including padding,
  `display`, or `margin: 0 auto`, fails rule 4.
- Check authored HTML and CSS anywhere inside the student's assignment folder,
  including `<style>` blocks. Do not treat utility classes or browser defaults
  as substitutes.
- Ignore `_ref/`, generated dependencies, and instructor-provided
  `DO_NOT_CHANGE.css` files.

## Workflow

1. Get the assignment directory (`eNN` or `qNN`) from the request. If it is not
   clear, ask for it.
2. Read the roster from `landing/public/data.json`.
3. Match student directories case-insensitively to roster `firstName` values.
4. Treat a student as missing when no matching directory exists or their
   directory contains no authored HTML file.
5. Before assigning any punishment for missing work, show all missing students
   and ask the user which of them informed the user. Do not infer this from
   assignment status or prior data.
6. Do not punish missing students who informed the user. Add one punishment to
   each missing student who did not inform the user. Do not also apply the four
   code rules to a missing submission.
7. Audit each submitted project against all four rules. Record file and line
   evidence for every failure.
8. Present a concise proposed result showing each student, failed rule numbers,
   missing-submission status, and punishment increment.
9. Update only `pizzaSlices` in `landing/public/data.json`, preserving its
   formatting and all unrelated data.
10. Verify that every changed count increased by exactly the proposed amount,
    then report the changes and evidence.

If there are no missing submissions, skip the informed-student question. Never
decrease or reset an existing `pizzaSlices` value.
