# Home Dashboard Redesign Brief

## Current State

The existing "dashboard" at `[idOrSlug]/page.tsx` is a **podcast picker grid** — cards with cover art, title, description, category badge, author, and date. It's not a dashboard at all. It's a workspace landing page that lists your podcasts and lets you add new ones.

**File:** `apps/web/src/app/(routes)/(protected)/(workspace)/[idOrSlug]/_components/podcasts-cards.tsx`

---

## What We Built in the Prototypes

Two fully-refined standalone analytics pages deployed at [flightcast-preview on Vercel](https://github.com/artisticmedic/flightcast-preview):

### Overview Page — Show-Level Analytics
A single-show analytics dashboard. Everything answers: **"How is my show performing?"**

| Section | Chart Type | What It Answers |
|---------|-----------|-----------------|
| Loyalty Funnel | Trapezoid funnel (SVG) + legend table | How many listeners come back for 2nd, 3rd, Nth episode? |
| Biggest Drop-off Points | Background-bar rows (red) | Where exactly are we losing people in the funnel? |
| Best Times & Seasons | Icon + text insight rows + day-of-week bar chart | When should we publish? When do people listen? |
| Top Episodes Driving Loyalty | Background-bar rows (purple) | Which episodes do loyal listeners watch most? |
| Loyalty Trend | Line chart with area fill | Is our superfan audience growing over time? |
| Episode Breakdown | Data table with pagination | Per-episode metrics (listens, streams, views, downloads) |
| Top Platforms | Horizontal bars with platform icons | Where are people listening? (YouTube/Spotify/RSS split) |
| Geography | Horizontal bars with opacity gradient | Where are listeners geographically? |
| Demographics & Traffic | Horizontal bars with tab filters | Device/app/source breakdown |
| Audience Overlap | Empty state (coming soon) | Cross-episode listener overlap |

### Clusters Page — Listener Behavior Analytics
Answers: **"How do our listeners behave across topics and episodes?"**

| Section | Chart Type | What It Answers |
|---------|-----------|-----------------|
| Topic Overlap Heatmap | 6x6 grid with color tiers + platform filter | Which topic pairs share the most listeners? |
| Strongest Connections | Background-bar rows (purple) | Top cross-topic listener pairs ranked |
| Topic Affinity | Vertical bar chart with dropdown | If they watch X, what else do they watch? |
| Frequently Watched Together | 2x2 episode card grid with thumbnails | Which episodes cluster together? |
| Episode Clusters Table | Data table | Cluster summary (episodes, listeners, overlap, growth) |
| Also Watched | Background-bar rows (purple) | Per-episode: what else did these viewers watch? |
| Gateway Episodes | Background-bar rows (green) | Which episodes convert first-timers into returning listeners? |

---

## Design System Extracted

### Tokens
```
--font:            'Manrope', -apple-system, BlinkMacSystemFont, sans-serif
--text-primary:    #171717
--text-secondary:  #525252
--text-tertiary:   #7f7f7f
--accent:          #7857d1
--accent-light:    #f3f0fb
--accent-muted:    #ebe5f7
--success:         #30a84a
--danger:          #d5432c
--border:          #e5e5e5
--border-light:    #f0f0f0
--bg-muted:        #f5f5f5
--radius-lg:       6px
```

### Typography Hierarchy
| Role | Size | Weight | Color |
|------|------|--------|-------|
| Page title | 20px | 600-700 | primary |
| Card title | 16px | 600 | primary |
| Card description | 13-14px | 400 | tertiary |
| KPI value | 26px | 700 | primary |
| KPI label | 13px | 600 uppercase | tertiary |
| Table header | 13px | 600 uppercase | tertiary |
| Table cell | 15px | 400 | primary |
| Badge | 13px | 600 | varies |
| Body text | 14-15px | 500 | secondary |

### Card Pattern
- White background, 1px border `--border`, 6px radius, 24px padding
- Header: title (16px/600) + description (13px/tertiary) + optional actions
- Footer: border-top, 13px tertiary text + accent link

### Visualization Components (Reusable)

**1. Horizontal Bar (`.hbar-*`)**
Rank + icon + label + track + value. 8px track, animated fill.

**2. Background-Bar Rows (Style D)**
Used for: loyalty episodes, drop-offs, connections, gateway, also-watched.
Absolute-positioned colored background + overlaid content at z-index 1.
Color variants: purple (loyalty/connections), red (drop-offs), green (gateway/growth).

**3. Vertical Bar Chart**
Flex row of bars growing upward. Used for: day-of-week, topic affinity.

**4. Funnel**
SVG trapezoids with HTML label overlays. Legend table beside.

**5. Line/Trend Chart**
SVG with grid lines, area fill, dots, value labels. Stroke-dashoffset draw animation.

**6. Heatmap**
CSS grid of fixed-size cells with tier coloring (high/medium/low). Tooltips on hover.

**7. Data Table**
Sortable headers, hover rows, pagination. Badge metadata above.

### Animation System
- **Scroll reveal:** `.reveal` class, IntersectionObserver-based, 600ms fade+slide
- **Data animations:** Bars animate width from 0, chart heights from 0, all on reveal
- **Row stagger:** 50ms base + 50ms per nth-child
- **Easing:** `cubic-bezier(0.25, 0.46, 0.45, 0.94)` for most, `cubic-bezier(0.16, 1, 0.3, 1)` for snappy pops

### Color Usage Rules
| Color | When to Use |
|-------|------------|
| Purple (#7857d1) | Primary actions, brand accent, loyalty/engagement metrics, active states |
| Black (#171717) | Primary text, active pagination, sidebar CTA |
| Green (#30a84a) | Growth, conversions, positive changes, gateway episodes |
| Red (#d5432c) | Drop-offs, churn, negative changes |
| Gray gradient | Geography/demographics bars (opacity stepping: 1 → 0.75 → 0.55 → 0.40 → 0.30) |

---

## Home Dashboard — What It Should Become

The home dashboard sits at the **workspace level** (above individual shows). It should answer: **"How is my podcast business doing right now?"**

### Suggested Sections (drawing from prototype patterns)

**Top: KPI Strip**
- Total listeners (all shows combined)
- Total episodes published
- Growth % (vs last period)
- Top-performing show this period

**Mid: Show Performance Grid**
- Each show as a card with mini-KPIs (listeners, growth trend sparkline, latest episode)
- Click through to show-level analytics (which is what the overview prototype represents)

**Bottom: Cross-Show Insights**
- Top episodes across all shows (table or background-bar rows)
- Platform breakdown (horizontal bars — YouTube/Spotify/RSS)
- Geographic overview
- Best publishing times (timing insight rows)

### Components to Reuse Directly
These patterns from the prototypes map 1:1 to dashboard needs:
- KPI card grid → workspace-level metrics
- Horizontal bar chart → platform/geography breakdown
- Background-bar rows → top episodes, top shows
- Data table with pagination → episode list across shows
- Timing insight rows → publishing recommendations
- Trend line chart → growth over time
- Badge system → episode status, show status

### What's Different from the Prototypes
- **Multi-show context** — prototypes are single-show; dashboard aggregates across shows
- **Real data** — prototypes use hand-crafted demo data; dashboard hits tRPC/API
- **React + Tailwind** — prototypes are vanilla HTML/CSS/JS; dashboard uses Next.js component system
- **Sidebar navigation** — prototypes have the sidebar in CSS but don't render it; the real app has its own nav shell

---

## Files Reference

| File | Purpose |
|------|---------|
| `~/projects/flightcast-preview/overview.html` | Show-level analytics prototype (source of truth for design) |
| `~/projects/flightcast-preview/clusters.html` | Listener clusters prototype |
| `~/projects/flightcast-preview/retention.html` | Episode retention prototype (in progress) |
| `apps/web/src/app/(routes)/(protected)/(workspace)/[idOrSlug]/page.tsx` | Current dashboard page (just renders PodcastsCards) |
| `apps/web/src/app/(routes)/(protected)/(workspace)/[idOrSlug]/_components/podcasts-cards.tsx` | Current podcast grid component |
