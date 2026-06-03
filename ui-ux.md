### instant-page-transitions:

1. loading.tsx skeleton screens — Next.js App Router streams the skeleton immediately while the server component resolves. Added for dashboard, client list, client detail, edit, and new pages. Shimmer animation uses existing CSS tokens so it works in both light and dark mode automatically.
1. Navigation progress bar — A thin 2 px primary bar at the top fires within one paint of a tap. It has an 80 ms delay so it's invisible on fast loads but reassuring on slow ones. Lives in the root layout, covers all routes.
1. Page enter animation — A 180 ms fade-in + 4 px slide-up on each page's root element. Applied with a CSS class, no library. The bottom nav stays static throughout.
1. Bonus: staleTime: 30_000 in TanStack Query so revisiting a page with fresh cached data renders instantly — no spinner, no flicker.