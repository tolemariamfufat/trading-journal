[]()distrobox-enter -n code-box -- emacsclient -e "(kill-emacs)"

Since you are a Linux enthusiast and a trader, you might occasionally find the Emacs server gets "tangled" if you do a heavy update. If things start acting strange, just run this on your host to reset the engine:

Bash
distrobox-enter -n code-box -- emacsclient -e "(kill-emacs)"

Then, the next time you type e or ec, it will perform a "Transformation" and restart a fresh, clean daemon for you.

SPC f p: File Private (Opens your config).

SPC .: Find a file.

SPC b b: Buffer Buffer (Switch open files).

SPC w w: Window Window (Switch between split screens).

Open a file.

Press SPC v to split the window vertically.

Press SPC o t (or your shortcut) to open a terminal in one of those windows.

Now you can have your trading script on the left and your live terminal execution on the right, switching between them with a simple Ctrl + h or Ctrl + l.
`Space` leader key intuitive, or are you mostly sticking to the standard `h`, `j`, `k`, `l` movements for now?

1. The "Teleportation" Keys (Navigation)
Standard Vim movement is great, but these allow you to jump across files and windows like a pro.

SPC g g (Jump to Definition): Put your cursor on a Python function or variable and hit this. It will teleport you to exactly where that code was defined.

SPC s s (Swoop/Search Buffer): This is like a superpower. Type this and start typing a word; it will show you every line in your current file containing that word. Press Enter to jump there.

SPC f r (Recent Files): Forget where you saved that trading script? This opens a list of the last 20 files you touched.

2. The "Structure" Keys (Coding & Projects)
Since you’re building a multi-layered trading system, you need to manage your "project" as a whole.

SPC p p (Switch Project): Doom automatically recognizes folders with a .git or distrobox.ini as projects. This lets you jump between your Trading bot and your Linux config instantly.

SPC o p (Project Sidebar): This opens neotree or treemacs on the left. It’s a visual tree of your files, perfect for when you want to see the "form" of your project.

SPC i p (Insert Python Snippet): If you are in a .py file, this helps you quickly drop in common code structures.

3. The "Executioner" Keys (Terminal & Git)
This is where your Distrobox setup really shines.

SPC g s (Magit Status): This is widely considered the best Git interface in existence. It makes staging, committing, and pushing your code a beautiful, visual process.

SPC o t (Open Terminal): This opens a popup terminal at the bottom of your screen. Since you are in a code-box, this terminal is already inside your container!

SPC c c (Compile/Run): Often mapped to run your current script. You can set this up to execute your Python trading bot with one shortcut.

4. The "Window Master" (Managing Space)
As you scale your complexity, you’ll want multiple things on screen:
SPC b k: kill current buffer
SPC w v: Split window Vertically (Chart on left, Code on right).

SPC w s: Split window Shorizontally.

SPC w h/j/k/l: Move focus between the split windows using Vim directions.

SPC w c: Close the current window.

The "Trader's Secret": Custom Keybindings
You can actually create your own shortcuts in your config.el. Imagine hitting SPC a t (for Action Trading) and having it automatically open your ICT watchlist script.

Pro Tip: If you ever forget a keybinding, just press SPC and wait one second. A menu (Which-Key) will pop up at the bottom and show you every possible follow-up command. It’s like a built-in map for your keyboard.