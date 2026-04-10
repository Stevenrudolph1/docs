## 2026-03-25
- **New product:** Xavigate Map Update v1 built end-to-end
  - Backend: intake schema, generation prompt, report generator, PDF renderer (diagnostics/products/map-update/)
  - Frontend: map-update-intake.html (3-step wizard intake form)
  - API: /map/update-points, /intake/map-update endpoints added to shop worker
  - Database: map_updates + map_summary_points tables (migration 017)
  - Stripe: map-followup checkout now redirects to update intake page

## Previous Entries

[Previous changelog entries would appear here]
