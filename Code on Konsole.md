systemctl --user unmask plasma-gmenudbusmenuproxy.service 

systemctl --user unmask plasma-xembedsniproxy.service

rpm-ostree upgrade

systemctl reboot

### Pane 1 (Left Side): The Structural Blueprint Map

This pane handles your macro lines in the sand. You should run **`liquidity_scanner.py`** and **`midnight_open.py`** here.

- **How to use it:** Before any session opens, run your `liquidity_scanner.py` to identify your key lookback levels (like those 40, 60, and 120 windows). Once those structural high/low levels are mapped, keep `midnight_open.py` running to print the true daily opening line.
    
- **The Goal:** This pane tells you _where_ the institutions are likely going to hunt for stops.
    

### Pane 2 (Top Right): The Live Execution Speedometer

This pane is for tracking real-time momentum when price hits the levels from Pane 1. Run **`session_impulse_tracker.py`** or **`opening_gap.py`** here.

- **How to use it:** Leave this blank or idling until 5 minutes before London or New York opens. The moment the clock strikes the session open, fire up `session_impulse_tracker.py`.
    
- **The Goal:** If Pane 1 shows price hitting a major liquidity pool, look at Pane 2 to see if the algorithmic velocity is flashing aggressive buy/sell strings. If you are trading indices, `opening_gap.py` runs here right at the opening bell to see if price is going to instantly fill the gap or run away from it.
    

### Pane 3 (Bottom Right): The Institutional Divergence Confirmation

This pane is exclusively reserved for **`smt_divergence_tracker.py`**.

- **How to use it:** Keep this running continuously. It does the heavy lifting of cross-checking your indices (`USTEC`, `US500`) and FX pairs (`EURUSD`, `GBPUSD`) against each other.
    
- **The Goal:** When Pane 1 tells you price is at a key level, and Pane 2 tells you the market has massive velocity, you look down at Pane 3. If it flashes an SMT alert (like a Bearish Disconnect or Bullish Accumulation), you have your final confirmation to execute your trade on your Exness terminal.
    

### What about the other files?

- **`news_radar.py`:** Run this first thing in the morning _before_ you open your grid just to note high-impact events, then close it so it doesn't waste screen space.
    
- **`gbp_usd_mo.py`:** Keep this as a specialized tool only when you are focused strictly on Cable (`GBPUSD`) trading windows.
    
- **`dashboard.py` / `scanner_dashboard.py`:** Use these when you want a quiet, bird's-eye view of the market overall, but stick to the tight 3-pane setup during your actual live execution hours.



### The Essential Tiling Shortcuts

By default, you can split your current view instantly using these global Konsole shortcuts:

- **Split Vertically (Side-by-side):** `Ctrl` + `(`
    
- **Split Horizontally (Top/Bottom):** `Ctrl` + `)`
    
- **Move Focus:** `Shift` + `Tab` (or just click into the pane)
    
- **Maximize/Toggle Current Pane:** `Ctrl` + `Shift` + `]` (Super helpful when you temporarily need to read a long log or trace error)
    
- **Close Active Pane:** `Ctrl` + `Shift` + `W`
### How to Save Your Custom Grid Layout

The absolute best part is that you don't have to manually split your panes every single time you launch Konsole. You can save your grid and reload it with a single command or shortcut.

**1.Create your perfect grid:**Manual Layout.

Open Konsole and use `Ctrl` + `(` and `Ctrl` + `)` until you have exactly the grid layout you want (e.g., one large left pane for coding/running scripts, and two smaller stacked panes on the right for system monitoring or running tests).

**2.Save the Tab Layout:**Export to JSON.

Go to the top menu bar and select **View** -> **Save Tab Layout**. Save the resulting `.json` file somewhere handy (for instance, create a directory like `~/.config/konsole-layouts/dev-grid.json`).

**3.Launch it on demand:**Terminal Automation.

You can now spin up this exact layout instantly from your runner script or launcher using the `--layout` flag:

Bash

```
konsole --layout ~/.config/konsole-layouts/dev-grid.json &
```

> **Pro Tip for Automation:** If you want specific panes to automatically open in certain directories or even execute startup files, you can open that `.json` layout file in your text editor. Konsole structures it cleanly, and you can append `"WorkingDirectory": "/path/to/your/code"` or `"Command": "your-script"` directly inside the specific structural widgets of the file!

