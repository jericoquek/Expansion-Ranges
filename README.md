[README.md](https://github.com/user-attachments/files/34303771/README.md)
# Expansion Ranges

A TradingView indicator (Pine Script v6) that boxes the consolidation that follows an expansion.

Ranges don't come out of nowhere. Price expands, then it pauses, and the pause holds until price breaks out of it. This indicator finds the expansion, draws the pause as two liquidity lines (REH and REL), and removes the range the moment price closes beyond its 1.272 extension. Only the live range is ever on the chart.

Current version: **v1.20**

---

## How a range is built

**Expansion.** Swings are taken on the chart timeframe at about 4 minutes either side of the swing candle (4 bars on 1m, 2 on 2m, 1 on 5m and above). A swing leg counts as an expansion when it is at least 2.5 times the median of the previous 10 legs.

**The box.**
- The extreme side starts at the expansion's high or low.
- The other side starts at the first pullback and follows price while the pullback extends. It only becomes the range edge once it has held for 30 minutes.
- Once each edge has held, it never moves. The box, its hidden midpoint and the 1.272 lines stay where they are.

**Closes, not wicks.**
- A candle that trades through REH or REL and closes back inside leaves the line where it is.
- A candle that closes beyond it breaks it. The line turns dashed, stops at that candle and gets a label for the type of break.
- No new line starts on a broken side. A broken box is expansion, not a wider range.

**Break labels.**
- `squeeze break`: the line was in a squeeze on the candle before the break.
- `sweep`: price ran in from the far quarter of the range within the last 6 candles.
- `tease`: neither of the above.

**Squeeze.** A line is in a squeeze when the last 5 candles all closed on its side of Moving average 1 (20 EMA by default), none of them traded through the line, and all stayed in its half of the range. The line thickens and its label reads `REH · squeeze`.

**Exit.** The 1.272 lines are the extension of each half of the box from the midpoint. The range ends on the first candle that closes beyond either one. A new 2.5x expansion that closes beyond the range's REH or REL also ends it.

**What comes next.**
- After an exit, the breakout leg becomes the next expansion if it is at least a normal swing. That's measured from the swing where it started to its first confirmed extreme.
- A shorter breakout leg still counts if it keeps going from a higher low (or a lower high for a downside breakout).
- Otherwise the indicator waits for the next 2.5x expansion and draws nothing in between.

**Trend tag.** The labels read `REH · uptrend` / `REL · uptrend` when both of these hold:
- the range before this one ended upward;
- this range's REL, once it has held, sits above that range's low.

A close below this range's REL removes the tag. `downtrend` is the mirror image. The tag describes structure; it changes nothing else.

---

## Settings

**Display**

| Setting | Default | Notes |
|---|---|---|
| Show expansion leg | On | The leg that opened the range |
| Show 1.272 lines | On | The exit lines. Keep these on, they decide when a range ends |
| Show labels | On | |
| Moving average 1 | Off, EMA 20 | EMA or SMA, length, colour. Defines the squeeze whether it is plotted or not |
| Moving average 2 | Off, EMA 50 | Display only |

**Lines and labels:** colour and style for the expansion leg, the liquidity levels, the upper sweep and the lower sweep, plus the fib line colour. Level text defaults to `reh` / `rel` and can be changed to anything (REH/REL, EQH/EQL, H/L).

---

## Alerts

Create one alert on the indicator with **Any alert() function call**. Each type has its own tick box. Events on the same candle come as one message, so you get at most one alert per candle.

| Alert | Default | Fires when |
|---|---|---|
| Squeeze formed | On | A line goes into a squeeze. Once per line |
| Squeeze break | On | A line closes beyond while in a squeeze |
| Range ended | On | A close beyond a 1.272 line, or a new expansion through the range |
| New range | Off | A range opens |
| Tease break | Off | A line closes beyond with no squeeze and no run from the far side |
| Sweep break | Off | A line closes beyond after a run from the far quarter of the range |

An alert keeps the settings it was created with. If you change the tick boxes, delete the alert and create it again.

---

## What the labels did on my data

I tested this on several weeks of XYZ100 perp and MNQ data on 2m and 5m (Sep–Oct 2026).

| | Result |
|---|---|
| Squeeze break: price went half a range further before half a range back, measured from the breaking close | 54% |
| Tease break, same test | 54% |
| Sweep break, same test | 50% |
| Range ended in the direction of the expansion that created it | 65% of 2,419 ranges |
| After a range ended, price closed back inside the old box within an hour | 65% |
| Time a range is live on the chart | 72–79% |
| Median range length | 28–50 minutes |

The labels describe what happened. They are not signals. The 65% for the expansion's direction is the most useful number here, and the leg is drawn on the chart so you can see it.

---

## Notes

- Built and tested on 1m to 5m. Higher timeframes run, but I haven't tested them.
- Engine constants are fixed by design, so there are no tuning inputs.
- Only the live range is drawn. A range is removed on the candle that ends it.
- Swings confirm a few candles late, so a new range appears a few candles after its expansion high or low.
- The full changelog is in the header of the script.

## Install

Open the Pine Editor in TradingView, paste the script, save it, and click **Add to chart**.
