---
title: "How We Cut Dashboard Load Time by 40% at Vercel"
description: "A deep dive into the bundle analysis, code-splitting strategies, and rendering optimizations we used to make the Vercel Dashboard significantly faster."
date: "2025-11-15"
layout: "../../layouts/PostWrapper.astro"
tags: ["Performance", "Next.js", "React"]
---

# How We Cut Dashboard Load Time by 40% at Vercel

When your product is a platform that developers use to deploy fast websites, your own dashboard better be fast too. Earlier this year, our team set out to dramatically improve the Vercel Dashboard's loading performance. Here's what we did.

## The starting point

Our dashboard is a large Next.js application with hundreds of routes, complex state management, and real-time data streaming. Over time, the JavaScript bundle had grown significantly. Initial page loads were taking 3-4 seconds on mid-range devices.

## Step 1: Bundle analysis

We started by running `@next/bundle-analyzer` to understand what was actually being shipped to the client. The results were eye-opening:

- A charting library was being loaded on every page, even though only 3 routes used charts
- Date formatting utilities were duplicated across 4 different imports
- Several large icon sets were being fully imported instead of tree-shaken

## Step 2: Aggressive code-splitting

We moved to dynamic imports for anything not needed on first render:

```tsx
const LogViewer = dynamic(() => import('./log-viewer'), {
  loading: () => <LogViewerSkeleton />,
})
```

This alone removed 180KB from the initial bundle.

## Step 3: Server Components everywhere

We audited every component and moved everything possible to React Server Components. Forms, modals, and interactive elements stayed as Client Components, but layout, navigation, and data display all moved server-side.

## Results

After three weeks of focused work:

- **Initial JS bundle**: 1.2MB → 720KB (40% reduction)
- **Time to Interactive**: 3.4s → 2.0s on 4G
- **Lighthouse Performance**: 67 → 92

The key lesson: performance work isn't glamorous, but the compound effect of many small improvements is massive.
