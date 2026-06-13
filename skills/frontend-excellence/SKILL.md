---
name: frontend-excellence
description: Builds, reviews, and improves user interfaces to production quality. Use when building new UI, reviewing existing interfaces, auditing visual fidelity, improving interaction design, or when output must meet the bar of a design-aware senior engineer — not just "technically functional." Use when the difference between prototype and product matters.
---

# Frontend Excellence

## Overview

Ship user interfaces that look, feel, and behave the way production software at a quality company does. This skill applies the judgment of a senior design engineer across the full frontend stack: visual fidelity, interaction design, component architecture, accessibility, motion, and user perception. "It works" is not the bar. "A thoughtful user would not notice anything wrong" is the bar.

## When to Use

- Building new UI components, pages, or flows
- Reviewing UI before it is shown to users, stakeholders, or in a demo
- Auditing an existing interface for quality gaps
- Implementing designs from a Figma file or design spec
- Refactoring UI that has accumulated visual or UX debt
- Any time the output will be judged visually, not just functionally

**When NOT to use:** Pure data pipeline work, backend-only tasks, or CLI tooling with no visual surface. If nothing renders in a browser, this skill does not apply.

---

## The Core Standard

Apply this test to every UI decision:

> **"Would a user who cares about quality notice something is wrong here?"**

This catches more problems than any checklist. It forces the question to be answered from the user's perspective, not the implementer's. A misaligned icon, a missing hover state, a layout that breaks at 375px, a button that does not respond to keyboard — a quality-conscious user notices all of these.

---

## Phase 1: Understand Before Building

Before writing markup or styles, answer:

```
1. What is the user trying to accomplish on this screen?
2. What is the visual hierarchy? (What should the eye land on first?)
3. What states exist? (empty, loading, error, partial, full, disabled)
4. What interactions exist? (hover, focus, active, drag, swipe, keyboard shortcuts)
5. What does this look like at 320px, 768px, 1280px, and 1920px?
6. What happens if the content is twice as long as expected?
```

Do not proceed to implementation until these are answered. Missing a state at the planning stage costs 10× more to fix than catching it here.

---

## Phase 2: Visual Hierarchy and Layout

### The Hierarchy Decision

Every layout has an intended reading order. Enforce it with size, weight, space, and color — in that priority order.

```
Size and weight:   Primary action is visually largest or boldest
Space:             More space around important elements, less around secondary ones
Color:             Use color to reinforce hierarchy, not create it
Position:          Top-left to bottom-right (in LTR), with the most important elements first
```

### Spacing

Use a scale. Do not invent values. The exact scale depends on the project; the discipline does not.

```css
/* Good: values on a 4px scale */
padding: 8px 16px;
gap: 12px;
margin-top: 24px;

/* Bad: invented values */
padding: 11px 14px;
gap: 13px;
margin-top: 22px;
```

**Common spacing mistakes:**

| Mistake | Effect | Fix |
|---|---|---|
| Equal padding on all four sides of a card | Destroys visual hierarchy inside the card | Use less padding on top for titles, more on bottom for content |
| Consistent gap between all list items | Makes lists feel like walls of text | Use tighter gaps for related items, larger gaps for groups |
| No breathing room between sections | Page feels cramped and hard to scan | Increase inter-section spacing by 1.5–2× the intra-section spacing |
| Symmetric padding on buttons with icons | Icon and label look unbalanced | Add 2–4px extra padding on the icon side |

### Typography

```
h1: One per page. Page-level intent.
h2: Section headings. No more than 5–6 per page.
h3: Sub-section. Used sparingly.
body: Default reading text. 16px minimum on screen.
label/caption: 12–14px, never below 11px.
```

**Never:**
- Set `font-size` below 12px (causes squinting; fails accessibility at 11px and below)
- Use `font-weight: 500` where `400` or `600` would be clearer
- Mix more than two typefaces in a UI
- Use heading elements for styling, not semantics (`<h3>` because "I want it to look like an h3" is wrong)

### Color

```
Semantic tokens first:    --color-primary, --color-surface, --color-text-muted
Raw hex values:           Only in the token definitions, never in components
Contrast minimum:         4.5:1 for body text, 3:1 for large text and UI elements
```

**Color-only states are invisible to 8% of men and 0.5% of women.** Always pair color with a second signal:

```html
<!-- Bad: color is the only indicator -->
<span class="text-red-500">Required</span>

<!-- Good: color + icon + text -->
<span class="text-red-500 flex items-center gap-1">
  <ErrorIcon aria-hidden="true" />
  Required field
</span>
```

---

## Phase 3: Component Architecture

### The Right Unit Size

```
Too small:  <Label /> with no props — adds indirection with no abstraction value
Too large:  <UserDashboard /> that owns layout, data, auth, and rendering
Right:      <UserCard /> that renders one user with clear, stable props
```

**Rule:** A component is too large when it has more than one reason to change. Split it when:
- It handles both data fetching and rendering
- It has a prop interface with more than 6–8 props
- It is longer than ~150 lines

### Composition over Configuration

```tsx
// Bad: configuration prop explosion
<Modal
  title="Confirm Delete"
  confirmLabel="Delete"
  confirmVariant="danger"
  cancelLabel="Cancel"
  onConfirm={handleDelete}
  onCancel={onClose}
  showIcon={true}
  iconType="warning"
/>

// Good: composable structure
<Modal>
  <ModalHeader>
    <WarningIcon />
    Confirm Delete
  </ModalHeader>
  <ModalBody>Are you sure you want to delete this item?</ModalBody>
  <ModalFooter>
    <Button variant="ghost" onClick={onClose}>Cancel</Button>
    <Button variant="danger" onClick={handleDelete}>Delete</Button>
  </ModalFooter>
</Modal>
```

The composable pattern is more verbose at the call site but is easier to extend, test, and understand.

### Props That Age Well

```tsx
// Brittle: caller controls visual representation via data
<Button color="red" />

// Stable: caller expresses intent, component decides representation
<Button variant="danger" />
```

Design prop APIs for intent, not appearance. Appearance is an internal concern.

---

## Phase 4: All States Must Exist

This is the single most common failure mode in agent-generated UI. Every data-driven component has states beyond the happy path. All of them must be implemented.

```
State inventory for any data-driven component:

├── Loading          → skeleton or spinner (prefer skeleton for content areas)
├── Empty            → empty state with context and a call to action
├── Error            → error state with a recovery path (not just "Something went wrong")
├── Partial          → what happens if only some data is available?
├── Full / Populated → the intended working state
└── Disabled / Read-only → if applicable
```

### Loading States

```tsx
// Bad: blank screen while loading
function TaskList() {
  const { data } = useTasks();
  if (!data) return null;   // <-- invisible loading state
  return <ul>{data.map(...)}</ul>;
}

// Good: skeleton preserves layout during load
function TaskList() {
  const { data, isLoading } = useTasks();
  if (isLoading) return <TaskListSkeleton count={3} />;
  if (!data?.length) return <TaskListEmpty onAdd={handleAdd} />;
  return <ul>{data.map(t => <TaskItem key={t.id} task={t} />)}</ul>;
}

function TaskListSkeleton({ count }: { count: number }) {
  return (
    <ul aria-busy="true" aria-label="Loading tasks">
      {Array.from({ length: count }).map((_, i) => (
        <li key={i} className="h-14 animate-pulse rounded bg-muted" />
      ))}
    </ul>
  );
}
```

### Empty States

Empty states are features, not edge cases. They tell the user what the space is for and what to do next.

```tsx
// Bad: silent void
if (items.length === 0) return <div />;

// Good: purposeful empty state
if (items.length === 0) {
  return (
    <div className="flex flex-col items-center gap-4 py-16 text-center">
      <InboxIcon className="h-12 w-12 text-muted" aria-hidden="true" />
      <div>
        <p className="font-medium text-foreground">No tasks yet</p>
        <p className="text-sm text-muted">Create your first task to get started.</p>
      </div>
      <Button onClick={onCreateTask}>Create Task</Button>
    </div>
  );
}
```

### Error States

```tsx
// Bad: unhelpful generic message
if (error) return <p>Something went wrong.</p>;

// Good: actionable error with context and recovery
if (error) {
  return (
    <div role="alert" className="rounded-lg border border-destructive/30 bg-destructive/10 p-4">
      <p className="font-medium text-destructive">Failed to load tasks</p>
      <p className="mt-1 text-sm text-muted">{error.message}</p>
      <Button variant="outline" size="sm" className="mt-3" onClick={refetch}>
        Try again
      </Button>
    </div>
  );
}
```

---

## Phase 5: Interaction Design

### Hover and Focus States

Every interactive element must have a visible hover state and a visible focus state. These are non-negotiable — not because of accessibility rules (though that applies too), but because users learn what is clickable by what reacts to them.

```css
/* Bad: remove browser default and provide nothing */
button:focus { outline: none; }

/* Good: replace browser default with a styled equivalent */
button:focus-visible {
  outline: 2px solid var(--color-ring);
  outline-offset: 2px;
}

/* Good: hover communicates affordance */
.card:hover {
  box-shadow: 0 4px 12px rgb(0 0 0 / 0.08);
  transform: translateY(-1px);
  transition: box-shadow 150ms ease, transform 150ms ease;
}
```

### Click / Tap Targets

Minimum 44×44px touch target for all interactive elements (Apple HIG and WCAG 2.5.5 AAA). Small visual size is fine; expand the hit target with padding or `min-height`:

```css
/* Icon button that looks 24px but hits 44px */
.icon-button {
  display: flex;
  align-items: center;
  justify-content: center;
  min-width: 44px;
  min-height: 44px;
  padding: 10px;
}
```

### Transitions and Motion

Use motion to reinforce spatial relationships and causality — not for decoration.

```
Duration guide:
  Micro-interactions (hover, toggle):   100–150ms
  Appearing / disappearing elements:    200–250ms
  Page transitions:                     300–400ms
  Never:                                > 500ms for repeated interactions
```

```css
/* Good: short, purposeful */
.toast-enter {
  animation: slide-up 220ms cubic-bezier(0.16, 1, 0.3, 1);
}

/* Bad: slow and decorative */
.menu-item:hover {
  transition: all 0.8s ease;
}
```

**Respect `prefers-reduced-motion`:**

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

### Feedback for Actions

Every user action needs a response within 100ms. If the response takes longer, show a loading indicator. If it fails, show an error.

```
Action → Response timing:
  Visual response (hover, active state):    < 16ms   (synchronous, CSS)
  Optimistic UI update:                     < 100ms  (immediate, before server)
  Loading indicator trigger:                > 100ms  (after this, show spinner)
  Timeout / retry prompt:                   > 10s    (user should know it failed)
```

---

## Phase 6: Accessibility (WCAG 2.1 AA)

Accessibility is not a feature to add later. It is a correctness requirement, like type safety.

### Semantic HTML First

Use the element that is semantically correct. Reach for ARIA only when HTML has no appropriate element.

```html
<!-- Bad: div soup -->
<div class="btn" onclick="submit()">Submit</div>
<div class="heading">Section Title</div>
<div class="nav-item" onclick="navigate()">Home</div>

<!-- Good: semantic HTML -->
<button type="submit">Submit</button>
<h2>Section Title</h2>
<a href="/">Home</a>
```

### Keyboard Navigation

Tab through every interactive element in the finished UI. Verify:

1. All interactive elements receive focus in a logical order
2. Focus is always visible (never lost in a void)
3. Modal dialogs trap focus inside until closed
4. Focus returns to the trigger element when a modal or popover closes
5. Complex widgets (datepicker, dropdown, tabs) follow WAI-ARIA patterns for keyboard interaction

```tsx
function Dialog({ isOpen, onClose, triggerRef }: DialogProps) {
  const firstFocusableRef = useRef<HTMLButtonElement>(null);

  useEffect(() => {
    if (isOpen) firstFocusableRef.current?.focus();
  }, [isOpen]);

  useEffect(() => {
    if (!isOpen) triggerRef.current?.focus();
  }, [isOpen, triggerRef]);

  return (
    <dialog
      open={isOpen}
      onKeyDown={e => e.key === 'Escape' && onClose()}
      aria-modal="true"
      aria-labelledby="dialog-title"
    >
      <h2 id="dialog-title">Confirm action</h2>
      <button ref={firstFocusableRef} onClick={onClose}>Cancel</button>
      <button onClick={onConfirm}>Confirm</button>
    </dialog>
  );
}
```

### ARIA Labels

Label every interactive element that does not have visible text:

```html
<button aria-label="Close dialog"><XIcon /></button>
<input aria-label="Search" type="search" />
<nav aria-label="Main navigation">...</nav>
<section aria-labelledby="section-title">
  <h2 id="section-title">Recent Activity</h2>
  ...
</section>
```

### Live Regions for Dynamic Content

When content updates without a page navigation, announce it to screen readers:

```tsx
// Status messages (polite — does not interrupt)
<div aria-live="polite" aria-atomic="true">
  {statusMessage}
</div>

// Errors (assertive — interrupts immediately)
<div role="alert">
  {errorMessage}
</div>
```

---

## Phase 7: Responsive Design

### Mobile-First Approach

Write styles for the smallest viewport first. Add breakpoints as the layout needs to expand — not the other way around.

```css
/* Mobile first */
.grid {
  display: grid;
  grid-template-columns: 1fr;         /* 1 column on mobile */
  gap: 1rem;
}

@media (min-width: 640px) {
  .grid { grid-template-columns: repeat(2, 1fr); }
}

@media (min-width: 1024px) {
  .grid { grid-template-columns: repeat(3, 1fr); }
}
```

### Test These Widths

| Width | Represents |
|---|---|
| 320px | Smallest common phone (iPhone SE, Galaxy A series) |
| 375px | Standard phone width |
| 768px | iPad portrait, large phone landscape |
| 1024px | iPad landscape, small laptop |
| 1280px | Common laptop resolution |
| 1440px | Desktop |

### Content Overflow

Always test with content longer than you expect. Specifically:

```
- Names: "Bartholomew-Christophersen" (long compound name)
- Addresses: "4247 SE Belmont Street, Apartment 3B, Portland"
- Error messages that span 3 lines
- Empty states where the CTA text wraps
- Truncated text: always use `text-overflow: ellipsis` with `overflow: hidden` and `white-space: nowrap` together
```

---

## Phase 8: Visual QA Checklist

Run this before calling any UI task complete:

```
Layout
  [ ] No unintended horizontal scroll at any tested viewport
  [ ] Content does not overlap at any tested viewport
  [ ] Spacing is consistent with the design system scale
  [ ] Visual hierarchy matches the intended reading order

Typography
  [ ] No text below 12px
  [ ] Heading levels are semantic and not skipped
  [ ] Line length stays between 45–75 characters for reading text
  [ ] Text does not overflow its container

Color and Contrast
  [ ] All text passes 4.5:1 contrast (body) or 3:1 (large text, UI)
  [ ] No state is communicated by color alone

States
  [ ] Loading state is implemented and does not show a blank screen
  [ ] Empty state is implemented and is purposeful
  [ ] Error state is implemented and includes a recovery action
  [ ] Hover state is visible on all interactive elements
  [ ] Focus state is visible on all interactive elements

Interaction
  [ ] All interactive elements are keyboard accessible
  [ ] Touch targets are at least 44×44px
  [ ] Animations respect prefers-reduced-motion
  [ ] Actions that take > 100ms show a loading indicator

Accessibility
  [ ] Page has one <h1>
  [ ] Heading levels are sequential (h1 → h2 → h3, no skips)
  [ ] All images have alt text (or aria-hidden="true" for decorative images)
  [ ] All form inputs have associated labels
  [ ] All icon-only buttons have aria-label

Content
  [ ] No placeholder text (lorem ipsum) remains
  [ ] No hardcoded values that should come from data
  [ ] Numerical values are formatted (1000 → 1,000 or 1K depending on context)
  [ ] Dates are formatted consistently and localized if needed
```

---

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "Accessibility can be added later" | Focus management and semantic structure cannot be retrofitted without rewriting components. The cost multiplies by 5–10× after launch. |
| "The design isn't final, I'll style it properly then" | The unstyled version is what reviewers and stakeholders see. It sets expectations. Use the design system from day one. |
| "I only need the happy path for now" | Empty, loading, and error states are not edge cases — they are the first thing a new user sees. Every user encounters loading before they see data. |
| "The hover state is implicit" | It is not. Every user needs to learn what is clickable. Hover states teach them. |
| "This is just a prototype" | Prototypes become production. Prototypes that skip states, accessibility, and responsive behavior become production code with all those gaps intact. |
| "ARIA is complex, I'll skip it" | Most ARIA requirements reduce to: use semantic HTML, label icon buttons, announce dynamic content. Start there. |
| "The spacing looks fine to me" | "Looks fine" is not the same as "is consistent." Check the values against the design system scale. Invented values compound into visual chaos at scale. |

---

## Red Flags

- Blank screen or `null` return during loading
- Generic error messages with no recovery action
- Missing hover or focus states on interactive elements
- `outline: none` or `outline: 0` without a replacement
- Spacing values not on the project's scale (e.g., `margin: 13px`)
- `<div onClick>` instead of `<button>` or `<a>`
- `<h3>` used because "it looks right," not because it is semantically correct
- Color as the sole differentiator between states
- No empty state for data-driven lists or tables
- Animations with `transition: all` (animates unintended properties, causes jank)
- Touch targets under 40px in either dimension
- Mobile layout not tested below 375px
- Lorem ipsum or placeholder values left in place

---

## Verification

After completing any UI task, confirm:

- [ ] Visual QA checklist passed (see Phase 8)
- [ ] Renders without console errors or warnings
- [ ] All interactive elements reachable and operable by keyboard (Tab through the page)
- [ ] Screen reader announces page structure and dynamic changes correctly
- [ ] Tested at 320px, 768px, 1280px viewports
- [ ] All states implemented: loading, empty, error, populated
- [ ] Spacing, color, and typography follow the project's design system
- [ ] Motion respects `prefers-reduced-motion`
- [ ] No accessibility warnings from axe-core or browser DevTools accessibility panel

## See Also

For Core Web Vitals targets, bundle optimization, and image performance, see the `performance-optimization` skill.
For security-focused review of form inputs, authentication UI, and data exposure, see the `security-and-hardening` skill.
For browser-based runtime debugging and layout inspection, see the `browser-testing-with-devtools` skill.
