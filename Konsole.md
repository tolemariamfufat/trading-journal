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

