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

Copy and Tone
  [ ] No over-explanation — every sentence says the thing, not that it is about to say the thing
  [ ] Emoji used intentionally (0–1 per section), not decoratively on every item
  [ ] Error messages are specific: what failed + what to do next
  [ ] Empty states are short and action-oriented, not paragraph explanations
  [ ] CTA labels name the action ("Create project"), not just "Get Started"
  [ ] Placeholder text shows a real example, not a description of the field
  [ ] No AI generic phrases: "seamlessly", "unlock the power of", "take it to the next level"
  [ ] Bold used for one thing per paragraph, not scattered throughout
```

---

## Phase 9: Human-Like Copy and Content

AI-generated interfaces have a recognizable writing style. It signals to users that no human thought about this. Eliminate it entirely.

### The Content Standard

> **"Would a real person at a real company have written this?"**

If the answer is no, rewrite it. Copy is part of the UI. Bad copy makes a polished design feel broken.

### Emoji Usage

Emojis in UI are a tool, not decoration. Apply strict rules:

```
Allowed:
  ✓ One emoji in a headline to set tone (used intentionally, not habitually)
  ✓ Emoji in marketing/landing page hero text where personality is the goal
  ✓ Status indicators where emoji is the established convention (e.g. ✅ ❌ in CI logs)

Never allowed:
  ✗ Emoji at the start of every list item
  ✗ Emoji on every heading or section title
  ✗ Emoji in error messages (makes errors feel unserious)
  ✗ Emoji in form labels or input placeholders
  ✗ Multiple emoji in a single sentence
  ✗ Emoji used just to "break up the text"
```

**The test:** Remove the emoji. If the sentence loses nothing, the emoji added nothing. Remove it.

```
// Bad — emoji as decoration on every item
🚀 Get started quickly
⚡ Lightning fast performance
🔒 Secure by default
🎯 Built for teams

// Good — clean, confident copy that doesn't need decoration
Get started in minutes
Fast by default
Secure by default
Built for teams
```

### Font and Text Hierarchy

Do not use font weight or size as decoration. Every typographic decision must serve hierarchy.

```
// Bad — bold used for emphasis everywhere, creating visual noise
Track your <strong>tasks</strong>, manage your <strong>projects</strong>,
and <strong>collaborate</strong> with your team in <strong>real time</strong>.

// Good — bold used once, where it actually matters
Track tasks, manage projects, and collaborate with your team.
One place for everything your team ships.
```

**Rules:**
- Maximum two font weights in body copy (regular + bold/semibold)
- Bold is for the single most important word or phrase per paragraph — not every other word
- Italics are for citations, technical terms, or genuine emphasis — not style
- ALL CAPS is for labels and badges only — never for sentences or paragraphs
- Letter-spacing (`letter-spacing: wider`) is for short labels and badges, never for body text

### Over-Explanation

AI copy over-explains everything. Real product copy is direct and assumes the user is intelligent.

| AI Default | Human Version |
|---|---|
| "Welcome to your dashboard! Here you can see an overview of all your important metrics and data." | "Your dashboard" |
| "Click the button below to get started with creating your first project." | "Create your first project" |
| "This feature allows you to customize your settings according to your personal preferences." | "Customize your settings" |
| "No items have been added to this list yet. You can add items by clicking the button above." | "No items yet" + [Add item] button |
| "An error has occurred while processing your request. Please try again later." | "Couldn't save — try again" |
| "Are you sure you want to permanently delete this item? This action cannot be undone." | "Delete this project? This can't be undone." |

**The rule:** Say the thing. Do not explain that you are about to say the thing.

### AI Generic Symbols and Patterns

These patterns appear in nearly every AI-generated interface. They signal "an AI made this" immediately:

| Pattern | Why it is wrong | Fix |
|---|---|---|
| ✨ sparkle emoji on AI features | Overused to the point of self-parody | Use plain text or a custom icon |
| 🎉 party popper on success states | Infantilizes the user | "Saved" or "Done" is enough |
| "Unlock the power of..." | Marketing cliché | State what it actually does |
| "Seamlessly integrate..." | Means nothing | Describe the actual integration |
| "Take your X to the next level" | Empty superlative | Describe the actual improvement |
| Bullet lists of three with parallel "verb + noun" structure | Reads like AI output | Write actual sentences |
| Hero sections with "The [adjective] way to [verb] your [noun]" | Template headline | Write a headline about the specific product |
| Every CTA labeled "Get Started" | No differentiation | Name what they are starting ("Create project", "Connect account") |
| Feature cards with icon + title + two-sentence description, repeated 6 times | AI layout template | Let content drive layout |

### Empty State Copy

Empty states are the most over-explained part of any UI. Real product copy is short and action-oriented.

```
// Bad — AI empty state
📭 No messages yet!
It looks like you haven't received any messages yet.
Messages from your team and collaborators will appear here
once they start reaching out to you.
[Check back later]

// Good — human empty state
No messages
[Invite your team]
```

### Error Message Copy

Errors must be specific, honest, and give the user a path forward. Never use corporate passive voice.

```
// Bad — vague AI error
⚠️ An unexpected error has occurred.
We're sorry for the inconvenience. Please try again later or
contact support if the problem persists.

// Good — specific human error
Couldn't connect to the server. Check your connection and try again.
[Try again]  [Contact support]
```

### Placeholder Text

Placeholder text must show real examples, not describe the field.

```
// Bad — describes the field
<input placeholder="Enter your email address" />
<input placeholder="Type your message here..." />
<input placeholder="Search for items" />

// Good — shows a real example
<input placeholder="you@company.com" />
<input placeholder="What's on your mind?" />
<input placeholder="Search tasks, projects..." />
```
