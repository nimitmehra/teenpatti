# Teen Patti: Design Language

The approved look for everything in this folder (approved 2026-11-01).
- Visual reference: `style-guide-v2.html` (open it in a browser).
- Live implementation: `index.html` (CSS variables at the top of the `<style>` block).
- Earlier, rejected directions, kept for history: `style-guide.html` (walnut), `palette-study.html` (three palettes).

## The test

Dress the card table the way a centuries-old palace hotel would. Before adding anything, ask: would this have looked right 100 years ago, and will it still look right in 100 years? If it looks like a phone app, a casino or a dashboard, it fails.

## Materials (colours)

Think in materials, each with one meaning. Colour marks importance and role, never a player's status.

| Material | Hex | Meaning and use |
|---|---|---|
| Cream linen | `#F8F0DF` | The page. Never grey. |
| Ivory paper | `#FFFCF5` | Panels, plates, seats, inputs |
| Navy-black ink | `#121A2C` | All reading text |
| Warm grey | `#5A5246` | Secondary text |
| House navy | `#13254B` | The masthead (the only navy band), titles, outlined buttons, selected choices |
| Gold on navy | `#DDBF74` | Lettering on the navy masthead only |
| Bottle green | `#0E4A36` | Play: the one main move per screen, money won |
| Gold leaf | `#A3822F` | Honour: frames, rules, ornaments. Lines only, never text |
| Pale gold | `#F2E6C6` | Fills only the current player's seat and the winner's plaque |
| Antique gold | `#7A5C14` | Dark gold that may carry text: rank numbers, "Your turn", small-caps labels on ivory |
| Garnet | `#8B2630` | Caution only: Pack, money lost. As text or outline, never a filled box. No orange anywhere |
| Hairline | `#E4D7BC` | Lines between rows |
| Plate outline | `#C9BB9A` | Unselected plates and inputs |

Proportions on a typical screen: cream and ivory about 72%, navy 12%, ink 7%, green 5%, gold 3%, garnet 1%. Gold and green get more *presence* (every frame, every main button), not more *area*; a big green-and-gold field becomes casino felt.

Contrast (WCAG): ink on cream 15.3; ivory on navy 14.7; gold on navy 8.5; ivory on green 10.0; garnet on cream 7.7; antique gold on cream 5.5; ink on pale gold 14.0. Gold leaf on cream is 3.2, so it is used for lines only. Banned text pairings: gold leaf on ivory or cream; gold lettering on green.

## Typography

| Role | Font | Size | Notes |
|---|---|---|---|
| Whose turn / page titles | Cormorant Garamond 700 | 46–66px | Centred. One per screen |
| Money | Cormorant Garamond 700 | 30–46px | Always even-height, even-width figures (`lining-nums tabular-nums`). True minus sign |
| Section titles | Cormorant SC 700 | 21–25px | Capitals and small capitals, letter-spaced .14em, between two short gold rules |
| Labels and tags | Cormorant SC 700 | 15–18px | Letter-spaced .12–.14em |
| Anything you act on | Instrument Sans 500–600 | 18px and up | Buttons, explanations, body text |
| One flourish | Cormorant Garamond italic 600 | 23px | Only the winner's line |

Never: Cormorant below 20px (except the small-caps cut), italic on anything a player must read to act, letter-spaced lowercase, all-caps Cormorant without spacing, a headline whose last word sits alone (use `text-wrap: balance`). Browsers do not pass number settings into buttons, so set `font-variant-numeric` on `button` and `input` directly.

## Shapes

- Square corners: 2px at most (playing cards 4px).
- One frame per section: a gold double line (2px outer, 1px inner, set in 6–8px). No boxes inside boxes.
- Centred and symmetrical titles; the player's name on the centre line.
- Divider: a gold hairline with a small gold diamond in the middle. Bullets are the same diamond.
- One navy band: the masthead with the tabs. Section titles are navy lettering on ivory, so headings never look like buttons.
- Honour has a shape: a gold double frame on pale gold for the current player; an arched (palace-style) plaque for the winner. The arch is used nowhere else.
- Never: pills, pastel alert boxes, drop shadows, gradients, glows, emoji, icons, or a colour that changes with a player's status.

## Components

- **Main plate:** solid bottle green, ivory text, a gold inner line. At most one per screen, full width.
- **Plate:** ivory with a 1.5px navy outline, navy title, grey explanation line. Price written into the title: "Raise · 80".
- **Pack plate:** ivory with a garnet outline and garnet text.
- **Selected choice** (player count, house rules, chips, pot-limit winners): navy fill with ivory text.
- **Tag:** small capitals in ink inside a square hairline box. The same look for Blind, Seen, Packed and Dealer; the word does the work.
- **Seat:** an identical ivory tile for everyone; packed seats are dimmed to half strength.
- **Status line:** Pot, Blind pays and Seen pays as one centred row of large navy numbers, not dashboard tiles.

## Interaction and motion

- Hover: plate background one shade warmer (`#F7EFDD`); green deepens to `#0A3828`.
- Press: sinks 1px for an instant.
- Keyboard focus: a 3px gold leaf ring, offset 3px.
- Disabled: 40% strength, with the reason given in words nearby.
- Turn passes: the new name rises 8px and fades in over about 0.2s. The pot counts up to its new total in under half a second, and always lands on the exact figure.
- Nothing bounces, loops or flashes. If the device asks for reduced motion, everything switches instantly.

## Adding a new screen

1. Cream page, ivory frames, one centred title per frame.
2. Decide the single main action; make it the only green plate.
3. Colour only for meaning: navy for structure, gold for honour, green for play and gain, garnet for loss.
4. Check every new text colour pairing for at least 4.5:1 contrast.
5. Check it at 390px phone width: nothing spills sideways and every button is at least 54px tall.
