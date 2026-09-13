# Next.js Study Exercises

Progressive exercises based on what's already implemented in this repo
(App Router, 4 static pages, root layout with fonts + metadata, `Header`
with `next/link`, Tailwind v4, images in `public/images/`).

**How to use this file:**
1. Do the exercises in order, editing this repo.
2. After each exercise, write your answer in the `Your answer:` section
   (paste code, or explain in words what you did and why).
3. When finished, start a new session and ask the assistant to review your
   answers against the `What to check:` hints.

---

## Part 1 — Routing (you've seen the basics, deepen it)

### Exercise 1: Active link in the header
The `Header` doesn't show which page you're on. Highlight the current link
(e.g. bold or colored) based on the current URL.

**Requirements**
- Use `usePathname()` from `next/navigation`.
- `usePathname` only works in Client Components — think about what that
  means for `src/components/header.tsx`.

**Your answer:**
```
(Write here)
```

**What to check:**
- [ ] Did you add `"use client"` at the top of `header.tsx`?
- [ ] Is `Header` still imported from the (server) `layout.tsx`? (Yes, that's fine — a client component can be rendered by a server component.)
- [ ] What happens to the highlight on `/performance` vs `/performance-extra` if you compare with `===`? (That's a problem for Exercise 4.)

---

### Exercise 2: Nested routes
Create a product-style section:

```
src/app/products/page.tsx        → lists 3 hardcoded products (name + price)
src/app/products/[id]/page.tsx   → shows the product matching the id
```

**Requirements**
- In `[id]/page.tsx`, read the id with `params`.
- In Next 16 (React 19) `params` is **asynchronous**:
  `export default async function Page({ params }: { params: Promise<{ id: string }> })`
  — you must `await params`.
- List the 3 products on the index page with `Link` to each detail page.
- If the id doesn't match any product, call `notFound()`.

**Your answer:**
```
(Write here)
```

**What to check:**
- [ ] Did you `await params`? (Forgetting this is the #1 mistake in Next 16.)
- [ ] Does visiting `/products/999` trigger the 404 (Exercise 7 builds the 404 page — for now, Next's default 404 is fine)?
- [ ] Are the product names different on the index vs. detail page?

---

### Exercise 3: Search params
On the product list page (`/products`), support a `?sort=asc` or `?sort=desc`
query parameter and sort the list by price accordingly.

**Requirements**
- Read the query string with `searchParams` (also `Promise`-based in Next 16:
  `Promise<{ sort?: string }>`).
- The page becomes a `async` function.
- Add two links on the page: "Price ↑" and "Price ↓".

**Your answer:**
```
(Write here)
```

**What to check:**
- [ ] Did you `await searchParams`?
- [ ] Does changing the URL re-render the page with the new order?

---

### Exercise 4: Route groups + clean URLs
Right now `/products/1` is fine, but what if the site grows to have both
`/products` and, say, a marketing area? Practice route groups:

Move `products` into a group:

```
src/app/(shop)/products/page.tsx
src/app/(shop)/products/[id]/page.tsx
```

**Requirements**
- Routes must keep working (`/products/1` still resolves).
- The parentheses folder must **not** appear in the URL.

**Your answer:**
```
(Write here)
```

**What to check:**
- [ ] Did URLs change? (They shouldn't have.)
- [ ] Can you explain in one sentence what route groups are for? (Hint: grouping layouts/navigation without affecting the URL, e.g. `(auth)` vs `(dashboard)`.)

---

## Part 2 — Layouts, metadata & special files

### Exercise 5: Nested layout for the shop
Create a shared layout for the shop area with its own sidebar:

```
src/app/(shop)/layout.tsx   → renders <aside>Products</aside> + {children}
```

**Requirements**
- Use Tailwind classes to put the sidebar on the left and the page content on the right.
- The root `Header` (from `layout.tsx`) must still appear on every page.

**Your answer:**
```
(Write here)
```

**What to check:**
- [ ] Does the root layout still wrap everything? (Layouts nest: root → (shop) → page.)
- [ ] Can you name the difference between `layout.tsx` and `page.tsx`? (Layouts stay mounted between navigations; pages unmount/remount.)

---

### Exercise 6: Per-page metadata
The root layout sets `title: "Create Next App"`. That's wrong for every page.

**Requirements**
- Set a proper `title` and `description` for: home, `/performance`, `/reliability`, `/scale`, and `/products`.
- Practice **both** ways:
  - `export const metadata: Metadata = { ... }` in at least two pages
  - `generateMetadata()` (a `async` function returning `Metadata`) in the `[id]` product page, using the product name in the title.

**Your answer:**
```
(Write here)
```

**What to check:**
- [ ] Does the browser tab show the right title on each route?
- [ ] Is `generateMetadata` async? Does it receive `params`?

---

### Exercise 7: Special files (loading / error / not-found)
Create for the `(shop)` group:

```
src/app/(shop)/loading.tsx     → a simple spinner or "Loading…" skeleton
src/app/(shop)/error.tsx       → shows when a child throws (MUST be a client component)
src/app/(shop)/not-found.tsx   → custom 404 page
```

**Requirements**
- In `error.tsx`, add `"use client"` and call `reset()` from `next/navigation` in a "Try again" button.
- Test the 404 by visiting `/products/999`.
- Bonus: in the `[id]` page, `throw new Error()` temporarily to see `error.tsx` in action.

**Your answer:**
```
(Write here)
```

**What to check:**
- [ ] Does `error.tsx` have `"use client"`? (It must — it uses state/`reset`.)
- [ ] Where does `loading.tsx` show? (During client-side navigation and for Suspense boundaries — it does NOT show on the very first hard load in the same way.)

---

## Part 3 — Data fetching (the big Next.js shift: fetch on the server)

### Exercise 8: Fetch products from an external API
Replace the hardcoded products with real data fetched **on the server**:

**Requirements**
- Create `src/lib/products.ts` with a `getProducts()` and a `getProductById(id)` function that fetch from `https://jsonplaceholder.typicode.com/products` (or a small local JSON file in `src/lib/` if you prefer working offline).
- Call `getProducts()` directly inside the **server component** `page.tsx` (the page itself is async).
- On the detail page, call `getProductById()` and `notFound()` if nothing is returned.
- Do NOT add `"use client"` anywhere for this.

**Your answer:**
```
(Write here)
```

**What to check:**
- [ ] Where does the network request happen — browser or server? (Server. That's the point of server components.)
- [ ] Can you explain the difference between a Server Component and a Client Component in 2–3 sentences?

---

### Exercise 9: Caching & revalidation
Next.js caches fetches by default in the App Router. Make the behavior explicit:

**Requirements**
- In `getProducts()`, add `cache: "no-store"` to the `fetch` options, then
  try `next: { revalidate: 60 }` and explain the difference between the two.
- Read `revalidate`/`stale` behavior: after a 60s revalidation, what happens
  on the next request within that window?

**Your answer:**
```
(Write here)
```

**What to check:**
- [ ] Can you explain `cache: "no-store"` vs `revalidate: N` vs default (static) in your own words?
- [ ] Which would you choose for the product list of a real e-commerce site?

---

### Exercise 10: Loading state with Suspense
Wrap the product list in `<Suspense fallback={...}>` inside the products page
and give `loading.tsx` a chance to matter during navigation.

**Requirements**
- `import { Suspense } from "react"`.
- Put the part of the page that uses `await getProducts()` inside a child
  component (e.g. `ProductList`) and wrap `<ProductList />` in `<Suspense>`.

**Your answer:**
```
(Write here)
```

**What to check:**
- [ ] Why must the awaited part be in a child component? (Suspense only suspends its direct async subtree — the fallback shows around `<ProductList />`, not the whole page.)

---

## Part 4 — Client components & interactivity

### Exercise 11: Counter (first client component)
Create `src/components/counter.tsx`:

**Requirements**
- `"use client"` at the top.
- A button that increments a number using `useState`.
- Use it on the `/performance` page.

**Your answer:**
```
(Write here)
```

**What to check:**
- [ ] Why can't a server component use `useState`? (Servers don't have browser state between renders.)
- [ ] What is the boundary rule? (You can put a client component inside a server component, but not the other way around — a server component cannot be used *inside* a client component's JSX.)

---

### Exercise 12: Interactive product list (client + server together)
Build a **client** component `ProductFilter` that:

**Requirements**
- Receives the products as a **props** from the (server) products page.
- Has a text input that filters the list client-side as you type.
- The initial data still comes from the server (Exercise 8) — the client
  component only does filtering.

**Your answer:**
```
(Write here)
```

**What to check:**
- [ ] Data still fetched on the server? (Yes — props flow server → client.)
- [ ] What would be different if you fetched inside `ProductFilter` with `useEffect`? (Extra round-trip after hydration, possible flash of unfiltered/empty list — this is the "double fetch" anti-pattern.)

---

## Part 5 — Images & assets

### Exercise 13: Use your images with `next/image`
You have `public/images/{home,performance,reliability,scale}.jpg`. Show the
matching image on each page.

**Requirements**
- Use `next/image` (`<Image>`), NOT `<img>`.
- Set explicit `width`/`height` (or use `fill`).
- Add a `priority` prop on the home page image and explain what it does.

**Your answer:**
```
(Write here)
```

**What to check:**
- [ ] Can you name two things `next/image` does for you that `<img>` doesn't? (Auto optimization/resizing, layout shift prevention via dimensions, lazy loading, formats like AVIF/WebP.)
- [ ] What does `priority` do? (Eagerly loads the image, important for LCP images above the fold.)

---

## Part 6 — API / Route handlers

### Exercise 14: A simple JSON API
Create `src/app/api/products/route.ts` that:

**Requirements**
- `GET` returns the product list (reuse `getProducts()`).
- `POST` accepts `{ name, price }` in the JSON body, "creates" a product
  (you may keep an in-memory array with a module-level `let`), and returns
  the new product with status `201`.
- Return proper status codes and `Response.json(...)`.

**Your answer:**
```
(Write here)
```

**What to check:**
- [ ] Did you import the `Request`/`Response` web APIs and export `GET`/`POST` functions?
- [ ] Does the in-memory array survive between requests in dev? (Usually yes in dev, no in production serverless — mention the limitation.)
- [ ] How would you test it? (`curl`, or the browser, or a form from Exercise 15.)

---

### Exercise 15: Server Actions (form → action, no separate API)
Create a small form on the `/scale` page that "adds a team member" to an
in-memory list, using a **server action**:

**Requirements**
- A `"use server"` async function (in a separate file, e.g.
  `src/actions.ts`, exported).
- The form uses the `action` prop (no client-side submit handler).
- After the action runs, call `revalidatePath("/")` (or the specific path)
  and `redirect()` to refresh the page — the page re-renders server-side.
- Show the list of members on the same page (read from the same in-memory
  module in a server component).

**Your answer:**
```
(Write here)
```

**What to check:**
- [ ] Where does the action function execute? (Server, after a form POST — no client JS round-trip code needed.)
- [ ] Why `revalidatePath`? (To force the server component to re-run and pick up the new data instead of serving the cached render.)
- [ ] What's the difference between a Route Handler (Exercise 14) and a Server Action (this one)? (Route handlers are HTTP endpoints you can call from anywhere; server actions are functions invoked directly by forms/`useTransition`, part of the component tree.)

---

## Bonus (if you finish early)

### B1: Redirects & permanent redirects
Add to `next.config.ts`: `/perf` → `/performance` (308) and explain when
you'd use `redirect()` in code vs `redirects` in config.

### B2: Parallel routes / intercepting routes (read-only)
Just read the Next.js docs on "Parallel Routes" and "Intercepting Routes"
and write 2 sentences on when each is useful.

### B3: Fix the Tailwind v4 setup
`globals.css` uses `@tailwind base; @tailwind components; @tailwind utilities;`
(Tailwind v3 syntax). With Tailwind v4 the idiomatic entry is
`@import "tailwindcss";`. Change it, verify all styles still work, and note
what else changed in v4 (e.g. CSS-first config via `@theme`).

---

## Self-review checklist (before the review session)

- [ ] All parts 1–6 attempted (answers written in this file)
- [ ] `npm run build` passes without errors
- [ ] `npm run lint` passes
- [ ] You can explain in your own words: Server vs Client component, and
      `cache: "no-store"` vs `revalidate`
