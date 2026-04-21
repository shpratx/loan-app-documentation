# Tasheel Finance — Design System

## Color Palette

| Token        | Hex       | Usage                                      |
|--------------|-----------|---------------------------------------------|
| Primary      | `#1565C0` | Buttons, app bar, links, active states      |
| Secondary    | `#00897B` | Accent actions, success indicators, badges  |
| Error        | `#D32F2F` | Validation errors, destructive actions      |
| Surface      | `#FFFFFF` | Cards, sheets, dialogs                      |
| Background   | `#F5F5F5` | Screen background                           |
| OnPrimary    | `#FFFFFF` | Text/icons on Primary surfaces              |
| OnSecondary  | `#FFFFFF` | Text/icons on Secondary surfaces            |
| OnSurface    | `#212121` | Primary text on Surface                     |
| OnBackground | `#424242` | Secondary text on Background                |
| Divider      | `#E0E0E0` | Separators, borders                         |

## Typography

All type uses **Roboto** (LTR) / **Noto Sans Arabic** (RTL).

| Style    | Size | Weight   | Usage                                  |
|----------|------|----------|----------------------------------------|
| Display  | 34sp | Bold     | Hero numbers — loan amount, balance    |
| Headline | 24sp | SemiBold | Section titles — "Our Products"        |
| Title    | 20sp | Medium   | Card titles — "Cash Finance"           |
| Body     | 16sp | Regular  | Content text, descriptions             |
| Label    | 14sp | Medium   | Form labels, button text, captions     |

## Spacing

4dp base grid system:

| Token | Value | Usage                          |
|-------|-------|--------------------------------|
| xs    | 4dp   | Inline icon-to-text gap        |
| sm    | 8dp   | Between related elements       |
| md    | 12dp  | Card internal padding (tight)  |
| base  | 16dp  | Standard content padding       |
| lg    | 24dp  | Section separation             |
| xl    | 32dp  | Screen-level vertical spacing  |
| xxl   | 48dp  | Hero section padding           |

## Border Radius

| Element       | Radius |
|---------------|--------|
| Buttons       | 4dp    |
| Cards         | 8dp    |
| Modals        | 12dp   |
| Bottom Sheets | 16dp   |

## Elevation

| Level | dp  | Usage                        |
|-------|-----|------------------------------|
| 0     | 0dp | Flat surfaces                |
| 1     | 2dp | Cards at rest                |
| 2     | 4dp | Cards on hover/press         |
| 3     | 8dp | Bottom sheets, modals        |
| 4     | 16dp| Navigation drawer            |

## Component Inventory

### TasheelButton

| Variant   | Background | Text Color | Border          | Usage                    |
|-----------|------------|------------|-----------------|--------------------------|
| Primary   | Primary    | OnPrimary  | none            | Main CTA — "Apply", "Accept" |
| Secondary | transparent| Primary    | 1dp Primary     | Secondary actions — "Decline" |
| Text      | transparent| Primary    | none            | Tertiary — "Cancel", links |

States: `default` → `pressed` (8% overlay) → `disabled` (38% opacity)
Min width: 88dp · Height: 48dp · Horizontal padding: 16dp

### TasheelTextField

```
┌─ Label ──────────────────────────┐
│ [icon]  Placeholder / Value      │
├──────────────────────────────────┤
  Helper text / Error message
```

| State    | Border Color | Label Color | Helper Color |
|----------|-------------|-------------|--------------|
| Default  | Divider     | OnBackground| OnBackground |
| Focused  | Primary     | Primary     | OnBackground |
| Error    | Error       | Error       | Error        |
| Disabled | Divider 38% | OnSurface 38%| —           |

Height: 56dp · Corner radius: 4dp top only (outlined variant)

### ProductCard

```
┌──────────────────────────────────┐
│  [icon]  Product Title     →     │  ← Title style
│  Brief description               │  ← Body style
│  ┌────────┐ ┌────────┐          │
│  │ Min Amt│ │ Max Amt│          │  ← Label style
│  └────────┘ └────────┘          │
│  Rate: XX%  ·  Tenure: X–Xmo    │
│                                  │
│  [ Apply Now ]                   │  ← TasheelButton Primary
└──────────────────────────────────┘
```
Elevation: 1 · Radius: 8dp · Padding: 16dp

### StatusBadge

| Status     | Background   | Text Color | Label        |
|------------|-------------|------------|--------------|
| Active     | Secondary   | OnSecondary| "Active"     |
| Pending    | #FFA000     | #FFFFFF    | "Pending"    |
| Approved   | Secondary   | OnSecondary| "Approved"   |
| Rejected   | Error       | #FFFFFF    | "Rejected"   |
| Completed  | #757575     | #FFFFFF    | "Completed"  |

Radius: 12dp (pill) · Padding: 4dp vertical, 12dp horizontal · Label style text

### LoadingSkeleton

Animated shimmer placeholder matching the layout of the content being loaded. Uses `Background` color with 10% `OnSurface` overlay, animating left-to-right.

### ErrorState

```
┌──────────────────────────────────┐
│         [error_outline]          │  ← 48dp icon, Error color
│                                  │
│     Something went wrong         │  ← Title style
│   Please try again later.        │  ← Body style
│                                  │
│        [ Retry ]                 │  ← TasheelButton Primary
└──────────────────────────────────┘
```

## Icons

**Material Icons — Filled** variant throughout.

| Context          | Icon                  |
|------------------|-----------------------|
| Back navigation  | `arrow_back`          |
| Products         | `account_balance`     |
| Dashboard        | `dashboard`           |
| Loan detail      | `receipt_long`        |
| Payments         | `payments`            |
| Settings         | `settings`            |
| Biometric        | `fingerprint`         |
| Error            | `error_outline`       |
| Success          | `check_circle`        |
| Info             | `info`                |

## Accessibility

| Requirement              | Implementation                                              |
|--------------------------|-------------------------------------------------------------|
| Touch targets            | 48dp × 48dp minimum for all interactive elements            |
| Content descriptions     | `contentDescription` on all icons, images, interactive views |
| Screen reader            | Full TalkBack support; logical focus order                   |
| Dynamic text             | All text scales with system font size (sp units)             |
| Color contrast           | WCAG AA minimum — 4.5:1 body text, 3:1 large text           |
| Focus indicators         | Visible focus ring (2dp Primary) on keyboard/switch nav      |
| RTL support              | Full mirroring for Arabic locale                             |
| Motion                   | Respect `prefers-reduced-motion`; no essential animations    |
