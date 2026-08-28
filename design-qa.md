**Comparison Target**

- Source visual truth: `/home/etwyman/Developer/lerd/design-reference-herd-qa.png`
- Rendered implementation: `/home/etwyman/Developer/lerd/design-implementation.jpg`
- Side-by-side evidence: `/home/etwyman/Developer/lerd/design-comparison-final.png`
- Local route: `http://127.0.0.1:7073/#dashboard`
- Viewport: 1280 × 720 CSS px, device pixel ratio 1.6
- Source pixels: 1018 × 720. Implementation pixels: 1280 × 720.
- Normalization: both captures were fit into equal 1280 × 720 comparison cells without changing aspect ratio. The source is an official Herd Services screen in light mode; the implementation is Lerd's empty Dashboard in dark mode. The comparison therefore evaluates product language, density, hierarchy, navigation, controls, and surface treatment rather than false pixel-level parity between different products and states.
- State: desktop, dark theme, empty Lerd installation, core services unavailable.

**Full-view Comparison Evidence**

- Lerd now follows Herd's utility-app structure: a labeled left navigation column, compact top heading, restrained 1px dividers, low-radius controls, dense information rows, and flat content sections.
- The dashboard keeps Lerd's red identity and current dark theme while removing the previous gradient panel, large pill treatments, oversized radii, animated pings, and decorative status dots.
- The 1280 px view has no horizontal overflow. Dashboard cards use two tracks and preserve readable card heights; additional rows scroll vertically rather than being compressed or clipped.

**Focused Region Comparison Evidence**

- Sidebar: labels, 32 px navigation rows, compact icons, selection surface, divider rhythm, and bottom utilities were reviewed against Herd's settings sidebar.
- Header and controls: font scale, button height, border radius, neutral surfaces, and use of red only for primary actions were checked at native capture size.
- Cards and status rows: headers, footers, dividers, empty states, status labels, and health glyphs were checked at native capture size. No additional crop was needed because these regions are legible in the 2656 × 768 combined image.

**Required Fidelity Surfaces**

- Fonts and typography: passed. System/Inter stack, compact 10–17 px hierarchy, medium weights, antialiasing, and restrained tracking match the source's native utility density.
- Spacing and layout rhythm: passed. Sidebar width, 32 px navigation rows, 4–6 px radii, 1px dividers, compact padding, two-column grid, and vertical scrolling are consistent and unclipped.
- Colors and visual tokens: passed. Neutral surfaces dominate, gradients were removed, semantic color is restrained, and Lerd red remains an intentional product constraint.
- Image quality and asset fidelity: passed. Existing Lerd logo and icon component are retained; no placeholder art, generated assets, CSS drawings, or replacement brand marks were introduced.
- Copy and content: passed. Existing product copy and localization hooks are preserved.

**Findings**

- No actionable P0, P1, or P2 visual findings remain.
- [P3] The official reference is light while the captured Lerd state follows the user's active dark theme. This is an intentional theme difference, not a fidelity defect; the structural and component language is consistent across both.

**Primary Interactions Tested**

- Dashboard → Sites → Dashboard navigation.
- Dashboard return performs a clean pane remount and lands on `#dashboard`.
- Primary actions and empty-state controls remain present and accessible in the DOM.
- Browser console checked after a fresh load: no errors.

**Comparison History**

1. Initial capture found a P1 horizontal clipping problem from a three-column card grid and a narrow icon-only rail. Fixed with labeled navigation, a restrained two-column grid, and a bounded content width. Post-fix evidence showed the complete navigation and card tracks at 1280 px.
2. First revised capture found a P1 vertical compression problem at 720 px: dashboard rows were forced into the remaining viewport and card content was clipped. Removed viewport-filling grid constraints and restored readable minimum card heights with page scrolling. Post-fix evidence shows full first-row cards and natural continuation below the fold.
3. User feedback identified the decorative status dots and animated pings as visual noise. Removed dot rendering from shared status primitives and one-off uses; status is now communicated by plain labels, restrained check/warning glyphs, and existing controls.
4. Interaction QA found a P1 stale-pane issue when returning from Sites to Dashboard. Added synchronous route updates and an explicit dashboard remount on the topology change. Browser verification passed: `Dashboard → Overview → Dashboard` with the final URL `#dashboard`.
5. Console QA found a P1 null-state error when the empty API returned `php_fpms: null`. Normalized the widget's array access. A fresh browser load now reports zero console errors.

**Implementation Checklist**

- [x] Herd-like labeled navigation and compact shell
- [x] Flat surfaces, small radii, and restrained accent use
- [x] Dot-free status system
- [x] Responsive two-column dashboard without horizontal overflow
- [x] Readable vertical card flow at 720 px
- [x] Working primary navigation and clean dashboard remount
- [x] Production build and clean browser runtime

**Follow-up Polish**

- Optional P3: capture and tune the light theme against the official Herd light reference if light-mode parity becomes a priority.

final result: passed
