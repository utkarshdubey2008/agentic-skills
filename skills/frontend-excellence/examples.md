# Frontend Excellence: Examples

Concrete before/after examples used by the `frontend-excellence` skill. Reference these during implementation or review to calibrate quality expectations.

---

## Example 1: Task List — All States

A minimal but complete data-driven list component showing every required state.

```tsx
// TaskList.tsx — all states handled

type Task = { id: string; title: string; done: boolean };

function TaskList() {
  const { data: tasks, isLoading, error, refetch } = useTasks();

  if (isLoading) {
    return (
      <ul aria-busy="true" aria-label="Loading tasks" className="space-y-2">
        {[1, 2, 3].map(i => (
          <li key={i} className="h-12 animate-pulse rounded-md bg-muted" />
        ))}
      </ul>
    );
  }

  if (error) {
    return (
      <div role="alert" className="rounded-lg border border-destructive/30 bg-destructive/5 p-4">
        <p className="font-medium text-destructive">Could not load tasks</p>
        <p className="mt-1 text-sm text-muted-foreground">{error.message}</p>
        <button
          onClick={() => refetch()}
          className="mt-3 text-sm underline hover:no-underline focus-visible:outline-2"
        >
          Try again
        </button>
      </div>
    );
  }

  if (!tasks || tasks.length === 0) {
    return (
      <div className="flex flex-col items-center gap-3 py-16 text-center">
        <CheckSquareIcon className="h-10 w-10 text-muted-foreground" aria-hidden="true" />
        <div>
          <p className="font-medium">No tasks yet</p>
          <p className="text-sm text-muted-foreground">
            Create your first task to get started.
          </p>
        </div>
        <button
          onClick={onCreateTask}
          className="mt-2 rounded-md bg-primary px-4 py-2 text-sm font-medium text-primary-foreground hover:bg-primary/90 focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-primary"
        >
          Create Task
        </button>
      </div>
    );
  }

  return (
    <ul role="list" className="divide-y divide-border">
      {tasks.map(task => (
        <TaskItem key={task.id} task={task} />
      ))}
    </ul>
  );
}
```

**Why each state matters:**
- `isLoading`: First thing every user sees. Blank screen here signals a broken app.
- `error`: Will happen in production. Users need to know what went wrong and how to recover.
- `empty`: New users and filtered views hit this. It must tell them what to do next.
- `populated`: The "normal" state. Usually well-implemented. The others are what agents skip.

---

## Example 2: Icon Button With Accessible Label

```tsx
// Bad — invisible to screen readers, fails audit
<button onClick={handleClose} className="p-2">
  <XIcon className="h-4 w-4" />
</button>

// Good — labeled, sized for touch, visible focus ring
<button
  onClick={handleClose}
  aria-label="Close dialog"
  className="flex min-h-[44px] min-w-[44px] items-center justify-center rounded-md p-2 hover:bg-muted focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-ring"
>
  <XIcon className="h-4 w-4" aria-hidden="true" />
</button>
```

---

## Example 3: Form With Validation States

```tsx
// Input with label, error, and accessible state

function EmailField({ error }: { error?: string }) {
  const id = useId();
  const errorId = `${id}-error`;

  return (
    <div className="flex flex-col gap-1.5">
      <label htmlFor={id} className="text-sm font-medium">
        Email address
        <span className="ml-1 text-destructive" aria-hidden="true">*</span>
      </label>
      <input
        id={id}
        type="email"
        autoComplete="email"
        aria-required="true"
        aria-invalid={!!error}
        aria-describedby={error ? errorId : undefined}
        className={cn(
          "rounded-md border px-3 py-2 text-sm",
          "focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-ring",
          error
            ? "border-destructive bg-destructive/5"
            : "border-input bg-background"
        )}
      />
      {error && (
        <p id={errorId} role="alert" className="flex items-center gap-1.5 text-sm text-destructive">
          <AlertCircleIcon className="h-3.5 w-3.5 flex-shrink-0" aria-hidden="true" />
          {error}
        </p>
      )}
    </div>
  );
}
```

**What this does right:**
- Label is associated via `htmlFor` / `id`
- `aria-required` signals required fields semantically
- `aria-invalid` + `aria-describedby` connect the error to the input
- Error uses `role="alert"` so screen readers announce it when it appears
- Error icon is `aria-hidden="true"` — the text already conveys the error
- Required asterisk is `aria-hidden="true"` — not read as "asterisk" by screen readers

---

## Example 4: Responsive Card Grid

```tsx
// Mobile-first grid that adjusts column count by viewport

<section>
  <h2 className="mb-4 text-lg font-semibold">Recent Projects</h2>
  <ul
    role="list"
    className="grid grid-cols-1 gap-4 sm:grid-cols-2 lg:grid-cols-3"
  >
    {projects.map(project => (
      <li key={project.id}>
        <ProjectCard project={project} />
      </li>
    ))}
  </ul>
</section>
```

At 320px: 1 column, full width.
At 640px: 2 columns.
At 1024px: 3 columns.

**Why `ul` with `role="list"` on the `ul` element:** Some CSS resets (including Tailwind's) strip list semantics when `list-style: none` is applied. The explicit `role="list"` restores it.

---

## Example 5: Motion That Respects User Preference

```css
/* Base animation */
@keyframes slide-in {
  from { opacity: 0; transform: translateY(8px); }
  to   { opacity: 1; transform: translateY(0); }
}

.notification {
  animation: slide-in 220ms cubic-bezier(0.16, 1, 0.3, 1);
}

/* Disable for users who prefer reduced motion */
@media (prefers-reduced-motion: reduce) {
  .notification {
    animation: none;
    /* Optional: keep a non-motion alternative */
    transition: opacity 100ms ease;
  }
}
```

---

## Example 6: Anti-Pattern Gallery

These are patterns that agents commonly produce. Each one looks correct until it is tested with a real user.

### Spacer Divs

```html
<!-- Bad: div used for spacing -->
<div class="h-4"></div>
<p>Next section</p>

<!-- Good: spacing via margin or gap in the parent layout -->
<section class="mt-4">
  <p>Next section</p>
</section>
```

### Outline Removal Without Replacement

```css
/* Bad: removes focus indicator with no replacement */
* { outline: none; }

/* Good: removes browser default, adds custom indicator */
:focus-visible {
  outline: 2px solid var(--color-ring);
  outline-offset: 2px;
}
```

### Relying on `placeholder` as a Label

```html
<!-- Bad: placeholder disappears when typing; fails WCAG 1.3.1 -->
<input type="email" placeholder="Email address" />

<!-- Good: visible label persists -->
<label for="email">Email address</label>
<input id="email" type="email" placeholder="you@example.com" />
```

### `transition: all`

```css
/* Bad: animates every property including layout-triggering ones */
.card { transition: all 0.3s ease; }

/* Good: animate only composited, cheap properties */
.card { transition: box-shadow 150ms ease, opacity 150ms ease; }
```

### Nested Ternaries in JSX

```tsx
// Bad: impossible to read or test
const content = isLoading ? <Spinner /> : hasError ? <Error /> : items.length === 0 ? <Empty /> : <List />;

// Good: named conditions, readable logic
if (isLoading) return <Spinner />;
if (hasError)  return <Error error={error} onRetry={refetch} />;
if (!items.length) return <Empty onAdd={onCreate} />;
return <List items={items} />;
```

---

## State Machine Approach for Complex UI

For components with many states, enumerate them explicitly before implementation:

```
States for a file upload component:
  idle          → default, waiting for user action
  dragging      → user is dragging a file over the drop zone
  uploading     → file is in flight, progress known or unknown
  processing    → file uploaded, server is processing
  success       → upload and processing complete
  error-size    → file exceeded size limit
  error-type    → file type not accepted
  error-network → upload failed due to network
  error-server  → server rejected the file
```

This enumeration prevents the common mistake of treating "error" as a single state. Each error state needs a different message and different recovery action.
