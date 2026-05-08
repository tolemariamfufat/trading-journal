distrobox-enter -n code-box -- emacsclient -e "(kill-emacs)"

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