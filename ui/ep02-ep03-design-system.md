# EP-02/EP-03 Design System — Sprint 2 Components

---

## Tokens Update

All Sprint 1 tokens carry forward unchanged — including the 4dp spacing grid (4, 8, 12, 16, 20, 24, 32, 40dp) and radii (4dp buttons, 6dp cards, 8dp inputs, 12dp modals). All Sprint 2 components follow the same grid.

### New Color Tokens

| Token | Value | Usage |
|-------|-------|-------|
| Success-subtle | #E8FAF0 | Status timeline success background |
| Warning-subtle | #FEF3E2 | Status timeline warning background |
| Error-subtle | #FDECEC | Status timeline error background |

### New Dimension Tokens (all multiples of 4dp grid)

| Token | Value | Usage |
|-------|-------|-------|
| stepperHeight | 56dp (14×4) | StepperIndicator total height |
| timelineItemHeight | 72dp (18×4) | VerticalTimeline row height |
| hostedFieldHeight | 200dp (50×4) | HostedFieldContainer min height |
| checkboxSize | 24dp (6×4) | TasheelCheckbox box size |
| dialogRadius | 12dp (3×4) | TasheelDialog corner radius |

---

## Component Inventory

### Sprint 1 (carried forward, 9 components)

| Component | Type | Changes |
|-----------|------|---------|
| TasheelButton | Atom | No changes |
| TasheelTextField | Atom | No changes |
| TasheelCard | Atom | No changes — reused by SummaryCard |
| StatusBadge | Atom | No changes |
| LoadingSkeleton | Atom | No changes |
| ErrorState | Atom | No changes |
| OtpDigitInput | Atom | No changes |
| BottomNavBar | Atom | No changes |

Note: HandleUiState is an implementation utility (code-level), not a design system component.

### Sprint 2 (new, 11 components)

| Component | Type | Screen(s) |
|-----------|------|-----------|
| StepperIndicator | Molecule | Application form (all steps) |
| VerticalTimeline | Molecule | Assessment status |
| SummaryCard | Molecule | Review screen |
| OfferBreakdownCard | Molecule | Offer details |
| CountdownTimer | Atom | Offer acceptance |
| HostedFieldContainer | Organism | Payment screen |
| AutoSaveIndicator | Atom | Application form |
| DatePicker | Atom | Personal info, employment info |

---

## Atom Components (Sprint 2)

### CountdownTimer [NEW]
- **Display**: HH:MM:SS format, monospace typography (titleLarge, 22sp)
- **Color logic**: Primary when > 1hr, Warning when < 1hr, Error when < 5min
- **Size**: intrinsic width, height 32dp
- **Accessibility**: announces remaining time every 5 minutes; announces every minute when < 5min

### AutoSaveIndicator [NEW]
- **States**: Saving (spinner 16dp + "Saving…" labelSmall Neutral-400), Saved (✅ + "Saved" labelSmall Success), Error (⚠️ + "Save failed" labelSmall Error)
- **Size**: intrinsic width, height 20dp
- **Position**: top-right of form screens, padding-right 16dp, padding-top 8dp
- **Accessibility**: status change announced via live region

### DatePicker [NEW]
- **Base**: Material3 DatePicker dialog
- **Variants**: Full range (DOB — min age 18, max age 70), Salary date (day 1–28 only, no 29–31)
- **Trigger**: TasheelTextField (read-only) with calendar trailing icon
- **Accessibility**: labeled with field purpose, announces selected date on confirmation

---

## Molecule Components (Sprint 2)

### StepperIndicator [NEW]
- **Layout**: horizontal Row, height stepperHeight (56dp), full width, evenly spaced
- **Steps**: 4 circles (24dp diameter) connected by lines (2dp height, Neutral-200)
- **Step states**:
  - Completed: filled Primary background, ✅ checkmark icon (white, 14dp)
  - Active: filled Primary background, step number (white, labelSmall)
  - Upcoming: Neutral-400 outline (2dp border), step number (Neutral-400, labelSmall)
- **Labels**: labelSmall (11sp) below each circle, Neutral-900 for active/completed, Neutral-400 for upcoming
- **Connecting lines**: Neutral-200 default, Primary for completed-to-completed segments
- **Accessibility**: each step announces "Step N of 4, [label], [completed/current/upcoming]"

### VerticalTimeline [NEW]
- **Layout**: vertical Column, each item height timelineItemHeight (72dp)
- **Item layout**: icon (24dp) + vertical line (2dp width) on left, title + subtitle + timestamp on right
- **Item states**:
  - Pending: ⏳ icon, Neutral-400, line Neutral-200
  - InProgress: spinner (24dp, Primary), line Neutral-200
  - Success: ✅ icon, Success color, line Success
  - Failed: ❌ icon, Error color, line Error
  - Warning: ⚠️ icon, Warning color, line Warning
- **Typography**: title — bodyMedium Neutral-900, subtitle — labelSmall Neutral-600, timestamp — labelSmall Neutral-400
- **Vertical line**: connects icons top-to-bottom; last item has no trailing line
- **Accessibility**: each item announces "[title], [status], [timestamp]"

### SummaryCard [NEW]
- **Container**: TasheelCard (default variant)
- **Layout**: title row (titleSmall Neutral-900 + Edit IconButton trailing) + divider + list of key-value rows
- **Key-value row**: label (bodySmall Neutral-600) left-aligned, value (bodySmall Neutral-900) right-aligned, vertical padding 8dp
- **Edit button**: 24dp icon, Primary color, 48dp touch target
- **Accessibility**: card announced as "[title] summary, [N] items, edit button available"

### OfferBreakdownCard [NEW]
- **Container**: TasheelCard (default variant)
- **Layout**: vertical list of label-value rows separated by Divider (1dp, Neutral-200)
- **Row**: label (bodySmall Neutral-600) left-aligned, value (bodySmall Neutral-900) right-aligned, vertical padding 12dp
- **Total row**: bold (titleSmall Neutral-900) for both label and value, top border 2dp Neutral-200
- **PD22 compliance**: all cost rows rendered with equal font size and weight (no fine print)
- **Accessibility**: each row announced as "[label]: [value]"; total row announced as "Total: [value]"

---

## Organism Components (Sprint 2)

### HostedFieldContainer [NEW]
- **Layout**: Column, min height hostedFieldHeight (200dp), full width, border-radius 8dp, border 1dp Neutral-200
- **Loading state**: LoadingSkeleton centered within container while SDK initializes
- **Ready state**: WebView/iframe renders payment processor card fields (PAN, Expiry, CVV)
- **Error state**: ErrorState component shown if SDK fails to load, with retry
- **PCI-DSS badge**: Row at bottom — 🔒 icon + "Secured by [Processor]" (labelSmall Neutral-400), padding 8dp
- **Accessibility**: container labeled "Secure card entry", focus traps within hosted fields during input

---

## Additional Sprint 2 Components

### TasheelDropdown [NEW] — Atom
- **Purpose**: Single-select dropdown for Region, Employment Type, Salary Date
- **Variants**: Standard (fixed options), Searchable (for employer name)
- **States**: Default (border Neutral-400), Open (border Primary, dropdown visible), Selected (shows value), Error (border Error), Disabled (50% opacity)
- **Size**: Height 48dp, border-radius 8dp (radiusLg), follows TasheelTextField visual style
- **A11Y**: role=dropdown, announces "Select [label]", announces selected value, keyboard navigable

### TasheelCheckbox [NEW] — Atom
- **Purpose**: Confirmation checkbox on Review screen
- **States**: Unchecked (Neutral-400 border), Checked (Primary fill + white checkmark), Disabled (50% opacity)
- **Size**: Checkbox 24dp (checkboxSize), touch target 48dp (touchTarget)
- **Layout**: Checkbox + label text (bodyMedium) in Row, 8dp gap
- **A11Y**: role=checkbox, announces "checked/unchecked", label linked

### TasheelDialog [NEW] — Molecule
- **Purpose**: Confirmation dialogs for Decline Offer, Cancel Application, destructive actions
- **Variants**: Confirmation (primary action), Destructive (error-colored action)
- **Layout**: Title (titleMedium) + Message (bodyMedium, Neutral-600) + Actions row (secondary left, primary right)
- **Size**: Max width 320dp, padding 24dp, border-radius 12dp (dialogRadius)
- **Overlay**: Scrim 50% black
- **A11Y**: Focus trap, ESC/back to dismiss, announced as "dialog", auto-focus primary action
