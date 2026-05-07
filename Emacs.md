distrobox-enter -n code-box -- emacsclient -e "(kill-emacs)"

Since you are a Linux enthusiast and a trader, you might occasionally find the Emacs server gets "tangled" if you do a heavy update. If things start acting strange, just run this on your host to reset the engine:

Bash
distrobox-enter -n code-box -- emacsclient -e "(kill-emacs)"

Then, the next time you type e or ec, it will perform a "Transformation" and restart a fresh, clean daemon for you.