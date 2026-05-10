### Returning from `gd` (Go to Definition)

When you hit `gd`, Neovim often jumps you to a different line or even a different file. To "teleport" back to exactly where you were:

- **The Shortcut:** Press **`Ctrl` + `o`** (think "Jump **O**ut").
    
- **The Reverse:** If you want to jump forward again, press **`Ctrl` + `i`** (think "Jump **I**n").
### Returning from `K` (The Hover Window)

The hover window is "ephemeral," meaning it isn't a real file buffer.

- **Just Move:** Usually, just pressing `h`, `j`, `k`, or `l` to move your cursor will make the window vanish instantly.
    
- **If you got "Stuck" inside:** If you pressed `K` twice or clicked inside the window and your cursor is now trapped in the small box, press **`Esc`** or type **`:q`**. This closes the floating window and puts your cursor back in your code.
### Pro-Tip for your 1:00 AM Session

If you are deep in a trade and need to check a function but don't want to lose your place at all, **always try `K` first.** It’s less disruptive than `gd`.

If `K` doesn't give you enough info, then use `gd`, and remember that **`Ctrl` + `o`** is your "Emergency Exit" back to your main script.
### How to "Press" it

Once you save and restart Neovim:

1. Open your Python file.
    
2. Ensure you are in **Normal Mode** (press `Esc`).
    
3. Press **Space**, then quickly tap **l**, then tap **i**.
    
4. **The Result:** Look at the very bottom left of your screen. It should say: `LSP Status: Active (pylsp)`.