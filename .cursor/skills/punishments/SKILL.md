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
5. color values must not be used directly. Every color must be declared as a
   custom property on `:root` and used from there with `var(--name)`

Count a rule only once per student, even when it has multiple violations. A
student can therefore receive at most five code punishments for one assignment.
Rule 5 starts after e06. Do not apply it when auditing e06 or any earlier
assignment.

Interpret the rules literally:

- `title=""` and `alt=""` satisfy attribute presence.
- The universal selector must declare `box-sizing: border-box`.
- A `.container` rule must contain exactly one `width` declaration and one
  `margin: auto` declaration. Any other declaration, including padding,
  `display`, or `margin: 0 auto`, fails rule 4.
- A color value is a hex color, a color function (`rgb`, `rgba`, `hsl`,
  `hsla`, `hwb`, `lab`, `lch`, `oklab`, `oklch`, `color`, `color-mix`), or a
  CSS named or system color, including `transparent`. `currentcolor`,
  `inherit`, `initial`, `unset`, `revert`, and `revert-layer` are not color
  values.
- The only legal place for a color value is the value of a custom property
  (`--*`) in a rule whose selector is exactly `:root`. A raw color on any
  other declaration fails, including a non-custom property inside `:root`, a
  custom property on any other selector, a `var()` fallback, a gradient, a
  shadow, or a shorthand such as `border`.
- Using `var(--name)` satisfies the rule. A project with no color values
  passes rule 5.
- Color utilities and arbitrary color classes (`bg-white`, `text-black`,
  `bg-red-500`, `text-[#333]`) are direct color values and fail rule 5.
  Presentational color attributes in authored HTML (`color`, `bgcolor`,
  `fill`, `stroke`) fail too. Colors inside standalone SVG or image assets
  do not.
- Check authored HTML and CSS anywhere inside the student's assignment folder,
  including `<style>` blocks and `style` attributes. Do not treat utility
  classes or browser defaults as substitutes.
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
   each missing student who did not inform the user. Do not also apply the code
   rules to a missing submission.
7. Audit each submitted project against every rule that applies to that
   assignment. Record file and line evidence for every failure.
8. Present a concise proposed result showing each student, failed rule numbers,
   missing-submission status, and punishment increment.
9. Update only `pizzaSlices` in `landing/public/data.json`, preserving its
   formatting and all unrelated data.
10. Verify that every changed count increased by exactly the proposed amount,
    then report the changes and evidence.

If there are no missing submissions, skip the informed-student question. Never
decrease or reset an existing `pizzaSlices` value.
