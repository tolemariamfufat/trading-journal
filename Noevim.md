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
### Verification:

- **File Tree:** Press **`Ctrl + n`**. Does the sidebar appear on the left?
    
- **Terminal:** Press **`Shift + T`**. Does the terminal open at the bottom?
    
- **Trading:** Inside that terminal, type **`report`**.
### 📂 Opening Files

- **`<CR>` (Enter)**: Open the file in the current window.
    
- **`v`**: Open the file in a **Vertical Split** (side-by-side).
    
- **`x`**: Open the file in a **Horizontal Split** (top-bottom).
    
- **`t`**: Open the file in a **New Tab**.
### File Management

- **`a`**: **Add** a new file or directory (add a `/` at the end for a folder).
    
- **`d`**: **Delete** a file (it will ask for confirmation).
    
- **`r`**: **Rename** a file.
    
- **`x` / `c` / `p`**: **Cut / Copy / Paste** files.
### Navigation & View

- **`H`**: Toggle **Hidden Files** (those starting with a `.`).
    
- **`R`**: **Refresh** the tree (useful if you created a file in the terminal).
    
- **`W`**: **Collapse** all open directories.
    
- **`E`**: **Expand** all directories.
    
- **`-`**: Move the directory root **Up** one level.
    
- **`q`**: **Close** the tree window.
    
- **`g?`**: Open the **Help** menu inside the tree to see all available keys.
### Pro-Tip for your Trading Workflow

If you want to quickly jump between your code and the file tree without using the mouse:

1. **`Ctrl + w` then `h`**: Move your cursor **left** into the tree.
    
2. **`Ctrl + w` then `l`**: Move your cursor **right** back into your code.