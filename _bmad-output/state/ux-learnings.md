---
version: 6
last_updated: 2026-06-09
entry_count: 17
---

# Sprout UX Learnings

Team-wide design knowledge accumulated across features. Read at session start.

---

## Patterns

### LRN-001 · Implicit label pattern for custom FormField components
**Category:** pattern · **Applies to:** prototype, handoff · **Confidence:** 2×
**Last seen:** company-profile-extended-fields (design-qa, 2026-06-09)

When building a custom `FormField` wrapper component, use the implicit label association pattern: wrap the `<label>` element around the `<slot />` rather than placing them as siblings. This avoids needing explicit `for`/`id` wiring across every usage site. The label element wrapping the form control is semantically valid and screen-reader compatible. Add `data-has-error` binding from the `error` prop so scroll-to-first-error can target the wrapper. This pattern applies to all field controls including file uploads — a bare `<p>` element is not a label and will not be announced by screen readers when the upload control receives focus.

---

### LRN-006 · Scroll-to-first-error requires data attribute on all validated fields
**Category:** pattern · **Applies to:** prototype · **Confidence:** 1×
**Last seen:** company-profile-extended-fields (design-qa, 2026-06-03)

When implementing scroll-to-first-error on save, the marker attribute (e.g., `data-has-error`) must be dynamically present on every validated field — not statically on the first field only. Use `:data-has-error="error ? '' : undefined"` driven by the error prop, and call `document.querySelector('[data-has-error]')` after `nextTick()` so the DOM reflects the post-validation state before scrolling.

---

## Anti-Patterns

### LRN-007 · Never use v-model + @update:modelValue for confirmation gates
**Category:** anti-pattern · **Applies to:** prototype · **Confidence:** 1×
**Last seen:** company-profile-extended-fields (design-qa, 2026-06-03)

In Vue 3, `v-model` commits the new value to the reactive source before `@update:modelValue` fires. If a confirmation dialog is shown inside that handler, the model has already changed — Cancel cannot revert it without extra state. Use the pending-value pattern instead: bind `:model-value` (controlled, read-only), intercept changes with `@update:model-value`, hold the proposed value in a `pendingX` ref, and only commit to the data object after the user confirms.

---

## Conventions

### LRN-009 · Audit for orphaned imports and unused component files after every extraction
**Category:** convention · **Applies to:** handoff · **Confidence:** 2×
**Last seen:** company-profile-extended-fields (handoff, 2026-06-09)

When extracting a large section from a screen component into a child component, the parent's import block is not automatically cleaned up. Unused imports (`TogeCheckbox`, `TogeSeparator`, file upload components, etc.) silently remain and mislead the developer about what the parent actually uses. After every extraction: (1) grep the parent template for each import and remove any that no longer appear; (2) search the entire codebase for any standalone component files that have zero import references — these are often leftover iterations that should be deleted. A file with no importers is dead code regardless of how polished it looks.

---

### LRN-010 · Never use JavaScript reserved words as Vue prop names
**Category:** convention · **Applies to:** prototype, handoff · **Confidence:** 1×
**Last seen:** company-profile-extended-fields (handoff, 2026-06-03)

Naming a Vue prop `as`, `in`, `for`, `class`, `key`, or any other JS/HTML reserved word creates confusion for developers and can cause subtle compiler or runtime issues in certain build contexts. Surfaced when a `KybSection` component used `as` for the Authorized Signatory prop. Use descriptive names: `authorizedSignatory`, `targetFor`, `htmlClass`.

---

## QA Recurring

### LRN-002 · divide-* utilities don't work with @layer components tokens
**Category:** qa-recurring · **Applies to:** prototype, design-qa · **Confidence:** 1×
**Last seen:** company-profile-extended-fields (design-qa, 2026-06-03)

`divide-weak`, `divide-base`, etc. are not valid Tailwind utilities. Toge border tokens (`border-weak`, `border-base`, `border-strong`) are defined in `@layer components`, not in `@theme`. Tailwind only generates `divide-*` from `@theme` color variables. Using `divide-weak` silently applies no border color. Fix: add `border-t border-weak` directly to each row element, or use `[&>*+*]:border-t` with an explicit inline CSS variable.

---

### LRN-005 · Icon-only action buttons consistently arrive below 44px minimum
**Category:** qa-recurring · **Applies to:** prototype, design-qa · **Confidence:** 4×
**Last seen:** company-profile-extended-fields (design-qa, 2026-06-09)

Icon-only buttons below 44×44px have been caught in four separate QA passes: the main screen, extracted panel components, inline table editing buttons, and bare `<button>` elements used as row actions. The `icon-small` size (28px) in TogeButton is the most common culprit. Always use `size="icon-large"` (44px) for actionable icon-only buttons. This applies to: table row actions, file upload controls (Replace, Remove), panel close buttons, dismiss buttons, and inline edit save/cancel. When building bare `<button>` elements (not TogeButton), set `class="h-11 w-11"` explicitly. The `icon-small` and `icon-medium` sizes are for badges and decorative contexts only.

---

## Token Violations

### LRN-003 · Use text-on-{family} for text on filled semantic surfaces
**Category:** token-violation · **Applies to:** prototype, handoff · **Confidence:** 1×
**Last seen:** company-profile-extended-fields (design-qa, 2026-06-03)

`text-white` is a primitive color class and fails token compliance on filled semantic surfaces. The design system provides `text-on-danger`, `text-on-brand`, `text-on-neutral` for on-fill text — these are purpose-specific and adapt correctly to dark mode. Rule: any button or surface using `bg-danger`, `bg-brand`, or `bg-neutral` as a fill must use the corresponding `text-on-*` token, not `text-white`. The same applies to hover states: `hover:bg-danger-hover` not `hover:opacity-90`.

---

### LRN-004 · text-base is both a color token and a font-size utility — never combine with text-strong
**Category:** token-violation · **Applies to:** prototype, handoff · **Confidence:** 3×
**Last seen:** company-profile-extended-fields (post-update design-qa, 2026-06-03)

`text-base` is defined in `@layer components` as `color: var(--text-base)` (mushroom-500 gray). Tailwind also defines `text-base` as `font-size: 1rem` in `@layer utilities`. Combining `text-base` with `text-strong` on the same element creates two conflicting color declarations. This violation has appeared in three separate QA passes across the same feature — in the main screen, in an extracted component, and in a new panel. It is written instinctively as a font-size class by mistake. Rule: never use `text-base` alongside any other color token. Use `text-strong` or `text-weak` for color; use `text-300`/`text-400` for explicit font sizes.

---

### LRN-011 · Inline row edit must guard against switching rows without saving
**Category:** qa-recurring · **Applies to:** prototype · **Confidence:** 1×
**Last seen:** company-profile-extended-fields (post-update design-qa, 2026-06-03)

When a table supports inline row editing, clicking a second row's edit button while one row is already being edited silently discards all unsaved changes in the first row. Guard the `startEdit` function: if `editingId` is already set, cancel the current edit first before opening the new one. This prevents invisible data loss that will be noticed immediately during stakeholder walkthroughs of the table.

---

### LRN-012 · Segmented toggles need role="radiogroup" + role="radio" + aria-checked
**Category:** convention · **Applies to:** prototype · **Confidence:** 1×
**Last seen:** company-profile-extended-fields (post-update design-qa, 2026-06-03)

A segmented control (two or more mutually exclusive mode buttons styled as a toggle bar) is semantically a radio group. Without ARIA roles, screen readers announce it as a series of unrelated buttons with no indication of mutual exclusivity or current selection. Add `role="radiogroup"` on the container and `role="radio"` + `:aria-checked="mode === value"` on each button. This applies to any Search/Create, List/Grid, Monthly/Weekly, or similar toggle pattern.

---

### LRN-008 · Use bg-surface-inverted/40 for overlay backdrops — not bg-black/40
**Category:** token-violation · **Applies to:** prototype · **Confidence:** 1×
**Last seen:** company-profile-extended-fields (design-qa, 2026-06-03)

Raw `bg-black/40` is a primitive color. Overlay backdrops (modals, confirms, drawers) should use `bg-surface-inverted/40` — this maps to `--surface-inverted` which is the design system's dark fill token and adapts to dark mode correctly. The `/40` opacity modifier works with `@theme inline` tokens in Tailwind v4.

---

### LRN-013 · Every section added to a feature-gated page must be placed inside the gate block
**Category:** anti-pattern · **Applies to:** prototype, design-qa · **Confidence:** 1×
**Last seen:** company-profile-extended-fields (design-qa, 2026-06-09)

When a page has a `v-if="isFeatureEnabled"` gate wrapping a group of sections, any new section tied to that feature must be added inside the `<template v-if>` block — never after its closing `</template>`. Adding after the block makes the section unconditionally visible to all users regardless of their feature flag, a silent privilege escalation that only surfaces in production with the flag off. Before adding any new section to a gated page, read the gate's scope against the PRD's product flag table.

---

### LRN-014 · Empty states must render the exact copy specified in the PRD — not just a CTA
**Category:** qa-recurring · **Applies to:** prototype · **Confidence:** 1×
**Last seen:** company-profile-extended-fields (design-qa, 2026-06-09)

A CTA button alone is not an empty state. If the PRD or Appendix specifies copy for an empty state (e.g., "No related companies added yet. Click '+ Add Company' to link your first company."), that exact copy must appear above the CTA. Missing copy is a Major finding in QA — it leaves users without context for why the list is empty and what action will fill it. Always check the PRD Appendix for specified empty-state strings before prototyping list and table sections.

---

### LRN-015 · Read the component's index.ts before using any Toge component — CVA silently drops unknown values
**Category:** qa-recurring · **Applies to:** prototype · **Confidence:** 1×
**Last seen:** company-profile-extended-fields (design-qa, 2026-06-09)

CVA (class-variance-authority) silently returns the default variant when an unknown `variant`, `tone`, or `size` value is passed — no console warning, no TypeScript error (unless strict prop types are declared). Passing `variant="ghost"`, `tone="default"`, `variant="outline"`, or `size="sm"` to `TogeButton` produces a visually unstyled button with no indication of failure. Valid values are only in the component's `index.ts`. Before using any Toge component, open its `src/components/ui/toge-*/index.ts` and read the `cva(...)` call to confirm every prop value is real.

---

### LRN-016 · Icon semantic family must match the surrounding container's token family
**Category:** convention · **Applies to:** prototype, design-qa · **Confidence:** 1×
**Last seen:** company-profile-extended-fields (design-qa, 2026-06-09)

An icon inside a surface or banner that uses the `information` token family (`bg-information-subtle`, `text-information-text`) must use an information-family icon (e.g., `InfoIcon`) — not a warning-family icon (`AlertTriangleIcon`). Mixing icon semantic families with mismatched surface families creates a conflicting signal: the container says "informational", the icon says "warning". Map each icon to the family whose metaphor matches the message: `InfoIcon` → information, `AlertTriangleIcon` → caution/warning, `XCircleIcon` → danger, `CheckCircleIcon` → success.

---

### LRN-017 · Large form screen split pattern: section components with (data, errors) props, screen owns validation
**Category:** pattern · **Applies to:** handoff · **Confidence:** 1×
**Last seen:** company-profile-extended-fields (handoff, 2026-06-09)

When a form screen exceeds ~80 template lines due to multiple independent form sections, split each section into its own component using this pattern: each section component receives `(data: T, errors: Record<string, string>)` as props and emits `touchField(field: string, isValid: boolean)`. The screen component retains all validation logic, the `errors` ref, the save/cancel handlers, and the submit flow. Section components never validate — they only emit `touchField` on blur so the screen can clear individual errors reactively. This avoids prop explosion while keeping a single source of truth for form state.
