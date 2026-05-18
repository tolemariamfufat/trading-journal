### Method 2: The Core Neovim Lifecycle (Suspend & Resume)

If you prefer to run `cargo run` in your actual native host terminal shell rather than splitting the editor screen:

1. While editing `src/main.rs`, press **`Ctrl-z`**.
    
    - _This instantly suspends Neovim and drops you right back out to your host bash/zsh prompt._
        
2. Run your test command:
    
    Bash
    
    ```
    cargo run
    ```
    1. When you're ready to jump right back into the file exactly where you left off, type:
    

Bash

```
   fg
```

_(Short for foreground)._ Neovim will snap right back onto your screen instantly.
### Returning from `gd` (Go to Definition)

### Summary of your "Fluid" Navigation:

|**Action**|**Key Combination**|
|---|---|
|**Open/Hide Terminal**|`Shift + T`|
|**Jump Up (to Code)**|`Ctrl + k`|
|**Jump Down (to Terminal)**|`Ctrl + j`|
|**Enter "Normal Mode"**|`Esc`|
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
vim.keymap.set('t', '<Esc>', [[<C-\><C-n>]], { silent = true })
This specific line of code is essentially a "teleportation" command that lets you escape the terminal and get back to your editor tools.

### The Breakdown

In Neovim, the terminal is "greedy." When you are inside it (Terminal Mode), it swallows every key you press (like `i`, `w`, or `Esc`) and sends them to the shell instead of the editor.

The only way Neovim allows you to "break out" by default is the chord **`Ctrl-\`** followed by **`n`**.

Here is what each part of that command does:

- **`'t'`**: This tells Neovim the mapping only applies to **Terminal Mode**.
    
- **`'<Esc>'`**: This is the trigger. You want the `Esc` key to be your way out.
    
- **`[[<C-\><C-n>]]`**: This is the "secret handshake." It tells Neovim: "When I hit Escape, pretend I actually hit `Ctrl-\` and then `n`".
    
- **`{ silent = true }`**: This prevents a message from popping up in the bottom command line every time you hit the key, keeping your workspace clean.