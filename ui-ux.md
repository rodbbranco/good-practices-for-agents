### instant-page-transitions

1. Purpose: make navigation feel immediate by showing lightweight placeholders, a subtle progress affordance, and a short, respectful enter animation. Reduce perceived latency without hiding real loading issues.

2. Skeletons (`loading.tsx`):

- Use Next.js App Router `loading.tsx` to stream skeletons while server components resolve. Keep skeletons lightweight and layout-matching to avoid layout shift.
- Reuse existing CSS tokens (spacing, radii, colors) so shimmer works in dark/light modes.
- Skeletons should not duplicate semantic content for screen readers — keep them decorative and ensure real content replaces them in DOM order.

3. Navigation progress bar:

- Global bar in root layout: 2px height, primary color, uses transform/scaleX for GPU-accelerated animation.
- Start policy: show after an `80ms` delay to avoid flicker on fast navigations; animate while navigation is in-flight and finish with a short ease-out.
- Accessibility: add `role="progressbar"` and update `aria-valuenow`/`aria-valuemin`/`aria-valuemax` where meaningful (or keep decorative for indeterminate loads).

4. Page enter animation:

- Use a single root animation: 180ms fade-in + 4px slide-up (ease-out). Apply via a small CSS class; avoid heavy JS-based transitions.
- Honor `prefers-reduced-motion: reduce` — skip or shorten animations.

5. Data & cache interaction:

- Combine skeletons with cached data: set `staleTime: 30_000` in TanStack Query so returning to pages shows fresh cached content immediately (no spinner).
- For interactive pages, hydrate server-fetched data or `prefetchQuery` on navigation to reduce empty states.

6. Implementation snippets

```css
.nav-progress {
	position: fixed; left: 0; top: 0; height: 2px; width: 100%;
}
.nav-progress__bar { transform-origin: left; transform: scaleX(0); transition: transform 250ms linear; }
.page-enter { opacity: 0; transform: translateY(4px); }
.page-enter[data-ready] { transition: opacity 180ms ease-out, transform 180ms ease-out; opacity: 1; transform: translateY(0); }
@media (prefers-reduced-motion: reduce) { .page-enter[data-ready] { transition: none; opacity: 1; transform: none; } }
```

```jsx
// Skeleton in loading.tsx
export default function Loading() {
	return <div className="skeleton" aria-hidden="true">/* lightweight blocks */</div>
}

// Usage: add `data-ready` when content is mounted to trigger enter animation
```

7. Performance & accessibility notes:

- Ensure skeletons are purely presentational (`aria-hidden`) so screen readers focus on real content.
- Avoid large DOM trees in skeletons; prefer a few rects that match layout.
- Test under throttled networks and Lighthouse; ensure progress bar doesn't cause layout reflow.

8. Next.js specifics and client transitions:

- Prefer server components for initial data fetch and stream skeletons. When client interactions update data, hydrate TanStack Query (`dehydrate`/`Hydrate`) or `prefetchQuery` to avoid flash.
- Use `startTransition` / `useTransition` for non-urgent UI updates so React can keep transitions smooth.

9. Testing and PR checklist:

- Visual: skeletons match final layout; no layout shift when content loads.
- Motion: respects `prefers-reduced-motion`.
- Performance: no unnecessary network refetches thanks to `staleTime` and cache hydration.
- Accessibility: skeletons hidden from AT; progress affordance labeled or decorative as appropriate.

Bonus: staleTime: 30_000 in TanStack Query so revisiting a page with fresh cached data renders instantly — no spinner, no flicker.

### components-cache

1. Purpose: ensure components use a shared cache layer so UI is fast, consistent, and avoids unnecessary loading states.

2. Recommendation: prefer TanStack Query (react-query) for client-side data fetching instead of calling `fetch` inside `useEffect` in components. Use server-side fetching in server components when appropriate, then hydrate client cache.

3. Patterns and APIs:

- Use `useQuery` for read data and `useMutation` for writes. Prefer `prefetchQuery`, `setQueryData`, and `invalidateQueries` to keep the cache in sync and avoid refetch storms.
- Do not put primary data fetching in component-level `useEffect` — that bypasses the global cache and causes repeated spinners and janky transitions.
- For immediate UI updates, use optimistic updates via `useMutation` + `onSuccess`/`onMutate` to call `queryClient.setQueryData`.

4. Recommended default settings (project-wide QueryClient defaults):

- `staleTime: 30_000` — short enough to reflect recent changes, long enough to prevent flicker when navigating back.
- `cacheTime: 300_000` (5 minutes) — keeps data available while users navigate.
- `refetchOnWindowFocus: false` — avoid surprise reloads; enable selectively for pages that require always-fresh data.
- `refetchOnReconnect: true` — recover stale data after network drops.

5. Next.js / App Router notes:

- In server components, fetch data on the server and stream UI (use `loading.tsx` skeletons). When the client needs to interact, hydrate TanStack Query with `dehydrate`/`Hydrate` or call `prefetchQuery` in a client entry point.
- Client components that read/write interactive data should be responsible for cache reads via `useQuery` rather than doing their own `fetch` calls.

6. Example: replacing `useEffect` + `fetch` with `useQuery`:

```jsx
// before (anti-pattern)
useEffect(() => {
	fetch(`/api/clients/${id}`).then(r => r.json()).then(setClient)
}, [id])

// after (recommended)
const { data: client, isLoading, isError } = useQuery(['client', id], () => fetch(`/api/clients/${id}`).then(r => r.json()), {
	staleTime: 30_000,
})
```

7. Migration checklist:

- Identify components using `fetch` + `useEffect` for data that should be cached.
- Introduce a central `QueryClient` with the recommended defaults and wrap the app with `QueryClientProvider`.
- Replace `useEffect` fetches with `useQuery` or move the fetch to a server component and hydrate.
- Add tests using `msw` to mock network + assert cache behaviour.

8. Testing and observability:

- Add unit/integration tests for optimistic updates and cache invalidation flows.
- Instrument slow queries in logs/telemetry and track frequency of refetches to tune `staleTime`.

9. Quick checklist for PR reviewers:

- Does the component use `useQuery`/`useMutation` instead of `useEffect` fetches?
- Are optimistic updates & `invalidateQueries` used where appropriate?
- Are defaults respected (`staleTime`, `cacheTime`) or explicitly justified?

Rationale: centralizing data fetching through a query/cache layer improves perceived performance, reduces duplicated network requests, and makes optimistic updates and cache invalidation explicit and testable.