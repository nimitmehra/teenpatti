# Teen Patti Scorer — Rules Study and Design

Status (2026-11-01): BUILT. House rules in section 5 approved as listed. App is `index.html` in this folder (double-click to open). 51 logic checks pass (worked example, show/side-show/pot-limit rules, guards, ~2,000 random hands with chip totals balancing). Screens checked at desktop and 390px phone width. Backup of first tested version: `backups/2026-11-01/index_v1.html`.
Possible next steps (not built): add a player mid-session; save/restore a session file; Muflis (lowest-wins) variant.

Update (2026-11-01, later): restyled to the approved heritage design ("palace hotel card table": cream, navy, gold leaf, bottle green, garnet; Cormorant Garamond + Cormorant SC + Instrument Sans; square corners, gold double frames). The rules are in `DESIGN-LANGUAGE.md`; the visual reference is `style-guide-v2.html`. Game logic was not touched (byte-for-byte check against `backups/2026-11-01/index_v2.html`), and all 51 tests still pass. The earlier design is in `backups/2026-11-01/index_v2.html`.

---

## 1. What Teen Patti is (verified rules)

Teen Patti ("three cards") is an Indian betting card game, close to the British game Three-Card Brag.

- **Players:** any reasonable number; best with about 4 to 7 (Pagat). Wikipedia lists max 10. Works with 3.
- **Deck:** normal 52 cards, no jokers. Ace is high, 2 is low.
- **Boot:** before every hand, every player puts the same fixed amount (the "boot", like an entry fee) into the pot (the pile of chips in the middle). The opening stake equals the boot.
- **Deal:** dealer gives each player 3 cards face down. Dealer moves one seat each hand. Play goes round the table starting with the player after the dealer.
- **Blind vs seen:** a player who has not looked at their cards is "blind". Once they look, they are "seen" (playing "chaal") for the rest of the hand. Seen players pay double what blind players pay.
- **Stake:** the current bet level, measured in blind terms.
  - Blind player pays the stake (call) or 2× stake (raise; the stake doubles).
  - Seen player pays 2× stake (chaal) or 4× stake (raise; the stake doubles).
- **Pack:** fold. You lose everything you have put in this hand.
- **Last one standing:** if everyone else packs, the last player takes the pot without showing cards.
- **Show:** allowed only when exactly 2 players remain. Both reveal; higher hand wins the pot.
  - Blind player asking: pays the stake.
  - Seen player asking, other player also seen: pays 2× stake.
  - Seen player cannot ask for a show against a blind player (must bet or pack).
  - Tie: the player who did NOT pay for the show wins.
- **Side show (compromise):** a seen player, after paying a chaal (2× stake), may ask the previous player still in the hand (who must also be seen) to compare cards privately. That player may accept or refuse. If accepted, the lower hand must pack. Tie: the player who asked packs. If refused, play continues.
- **Limits (common in apps and clubs, not in Pagat):** a chaal limit (max stake) and a pot limit. Typical online ratio: chaal limit = 128 × boot, pot limit = 1,024 × boot (example: boot 2, chaal 256, pot 2,048). When the pot limit is reached, everyone still in shows and the best hand wins.

## 2. Hand rankings (highest first)

| # | Name | Example | Ways out of 22,100 | Chance |
|---|------|---------|------|------|
| 1 | Trail / Trio (three of a kind) | A A A | 52 | 0.24% |
| 2 | Pure sequence (same-suit run) | Q♥ J♥ 10♥ | 48 | 0.22% |
| 3 | Sequence (run, mixed suits) | 7♠ 6♦ 5♣ | 720 | 3.26% |
| 4 | Colour (all one suit) | K♣ 9♣ 4♣ | 1,096 | 4.96% |
| 5 | Pair | 8 8 K | 3,744 | 16.94% |
| 6 High card | nothing above | A J 6 mixed | 16,440 | 74.39% |

Counts verified by computing all 22,100 three-card hands (scratch script). They match Wikipedia.

- Any hand of a higher type beats any hand of a lower type.
- Inside a type: compare the highest card, then next, then last. For a pair, compare the pair first, then the odd card.
- Sequences: A-2-3 and A-K-Q are both valid; K-A-2 is not a run. **Sources disagree on which is top:** Wikipedia says A-K-Q then A-2-3; Pagat says A-2-3 is top. House rule needed.
- Suits have no rank by default; identical hands split (house rules vary).
- Note: sequence beats colour in Teen Patti (opposite of 5-card poker), because a run is rarer in 3 cards.

## 3. Gaps in the original brief

1. There is no small blind / big blind in Teen Patti. The poker blinds are replaced by the **boot**, which everyone pays equally. "Blind" in Teen Patti means "betting without looking at your cards".
2. "Match / raise / fold" become: **Chaal** (match), **Raise** (double), **Pack** (fold), plus three moves not in the brief: **See cards**, **Side show**, **Show**.
3. Bet amounts depend on whether the player is blind or seen. The app must work these out, not the players.
4. Dealer rotation and turn order were not specified.
5. Starting chips, limits, and tie rules were not specified.
6. The app cannot see the cards. At a show, a human must tell it who won (or tap the cards into a checker).

## 4. The scorer design

### Start: Setup screen
- How many players (big tap buttons 2–8).
- Names, in seating order clockwise (order = turn order).
- Boot (default 10), starting chips (default 1,000), chaal limit, pot limit.
- House rules toggles (section 5).
- First dealer (default: player 1).

### Main: Table screen (one hand at a time)
- Top strip: Hand number, dealer, pot, current stake.
- Seats: each player's name, chips, Blind / Seen / Packed, amount put in this hand. Current player glows.
- Turn panel: "Asha's turn — Blind". Only legal moves shown, each with its exact cost on the button.
  - Blind: Blind (stake) · Blind raise (2×) · Look at cards · Pack · Show (only if 2 left).
  - Seen: Chaal (2×) · Raise (4×) · Side show with [previous player] · Show (only if 2 left and other is seen) · Pack.
- One-line plain-English hint under each button.
- Undo last move (always visible).
- Live log of this hand: every move with amount.

### End of hand
- Triggered by: everyone else packs / show / pot limit.
- Ask who won (and optionally the winning hand type, or use the card checker).
- Pot goes to the winner, chips update, hand saved to history, dealer moves on.

### Scoreboard screen (overall tally)
- Leaderboard sorted by net +/- (up = green, down = red), current chips.
- Per player: hands played, hands won, win %, biggest pot won, best hand made.
- Play style: % of turns played blind, times packed, raises, side shows won/lost, shows won/lost.
- Hand history: every hand with winner, pot, how it ended; tap to see that hand's full move log.
- Settle up: who pays whom, fewest transfers.

### Buttons always available
- Hand Rankings sheet: the 6 hands drawn as real-looking cards, with examples, chances, tie rules.
- Rules sheet: the rules above in plain words, with a worked example round.

### Behind the scenes
- Single file `index.html` in this folder. Double-click to open. No install.
- Auto-saves in the browser after every move; reopening continues the game.
- Download record (CSV, opens in Numbers/Excel).
- Balances may go below zero (no "all-in" complications); the scoreboard shows who is up and down.

### Look
- Deep emerald "felt" background, ivory panels, antique-gold accents, ruby for Pack.
- Elegant serif for headings and numbers, clean sans-serif for buttons.
- Buttons at least 72px tall, 20px+ text, lots of space. Works on laptop, iPad and phone.

## 5. House rules — defaults pending approval
1. A-K-Q is the top run, A-2-3 second (Wikipedia / most apps). Alternative: A-2-3 top (Pagat).
2. Boot 10, starting chips 1,000 each.
3. Chaal limit 128 × boot, pot limit 1,024 × boot (editable, can be off).
4. Side show allowed when you and the previous player are both seen.
5. No limit on how many turns a player may stay blind.
6. Identical hands at a forced show split the pot.
7. Include the optional card checker at show / side show.
8. Premium look for this app, overriding the plain explainer-page style rule (that rule is for explainer pages; this is a game app).

## Sources
- Pagat: https://www.pagat.com/vying/teen_patti.html
- Wikipedia: https://en.wikipedia.org/wiki/Teen_patti
- Limits example and side-show cost: web search summary (gamerules.com, globalgamenetwork.com, teenpatti helpdesk)
