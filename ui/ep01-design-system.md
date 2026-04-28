# EP-01 Design System — Sprint 1 Components

---

## Tokens (from BL11)

| Token | Value | CSS Variable |
|-------|-------|-------------|
| Primary | #4F6EF7 | --primary |
| Primary Hover | #3B5AE0 | --primary-hover |
| Primary Subtle | #EEF1FE | --primary-subtle |
| Neutral-100 | #EDEDF3 | --neutral-100 |
| Neutral-200 | #D5D7E2 | --neutral-200 |
| Neutral-400 | #858AAD | --neutral-400 |
| Neutral-600 | #5A5F7D | --neutral-600 |
| Neutral-900 | #292C3D | --neutral-900 |
| Success | #2ECC71 | --success |
| Warning | #F39C12 | --warning |
| Error | #E74C3C | --error |
| Surface | #FFFFFF | --surface |
| Background | #F4F5F9 | --bg |

Spacing: 4dp grid (4, 8, 12, 16, 20, 24, 32, 40)
Radii: 4dp (buttons), 6dp (cards), 8dp (inputs), 12dp (modals)

---

## Atom Components (Sprint 1)

### TasheelButton
- **Variants**: Primary (filled #4F6EF7), Secondary (outlined), Text (no background)
- **States**: Default, Pressed, Disabled (50% opacity), Loading (spinner replaces text)
- **Size**: min height 48dp, min width 100dp, padding 12dp×20dp
- **Accessibility**: contentDescription required, 48dp touch target
- **Typography**: labelLarge (14sp, medium weight)

### TasheelTextField
- **States**: Default (border #858AAD), Focused (border #4F6EF7 + 3dp glow), Error (border #E74C3C), Disabled (50% opacity)
- **Size**: height 48dp, border-radius 8dp, border 2dp, padding 20dp
- **Label**: labelMedium (13sp) above field
- **Error text**: labelSmall (11sp) below field, color Error
- **Accessibility**: label linked to input, error announced by TalkBack

### TasheelCard
- **Variants**: Default (border #D5D7E2), Highlighted (border #4F6EF7, used for BridgeNow)
- **Size**: full width, border-radius 6dp, padding 20dp, shadow 0 2dp 4dp rgba(0,0,0,0.08)
- **Accessibility**: card is clickable container, contentDescription summarises content

### StatusBadge
- **Variants**: Success (#E8FAF0 bg, #2ECC71 text), Warning, Error, Info (#EEF1FE bg, #4F6EF7 text), Muted
- **Size**: padding 3dp×10dp, border-radius 9999dp (pill)
- **Typography**: labelSmall (11sp, semibold, uppercase)

### LoadingSkeleton
- **Animation**: shimmer (Neutral-100 → Neutral-200 → Neutral-100), 1.4s cycle
- **Shapes**: rectangle (full width × 20dp), rectangle (70% × 16dp), rectangle (50% × 16dp)
- **Usage**: replace content cards during loading

### ErrorState
- **Layout**: centered, icon (⚠️ headlineLarge) + message (bodyMedium, Neutral-600) + retry button (TasheelButton Primary)
- **Accessibility**: error message announced, retry button focusable

### OtpDigitInput [NEW for Sprint 1]
- **Layout**: 6 individual boxes, 48dp×48dp each, 8dp gap
- **States**: Empty (border Neutral-400), Focused (border Primary), Filled (border Primary, text visible), Error (border Error)
- **Behaviour**: auto-advance on digit entry, auto-focus first box, backspace moves to previous
- **Accessibility**: "Digit 1 of 6" through "Digit 6 of 6"

### BottomNavBar [NEW for Sprint 1]
- **Items**: Home (🏠), Products (📋), Profile (👤)
- **Active**: Primary color icon + label. Inactive: Neutral-400
- **Size**: 56dp height, each item 48dp touch target
- **Accessibility**: each tab has label, selected state announced
