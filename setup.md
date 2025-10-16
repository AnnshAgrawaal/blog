# Kali Linux Setup

- Kali Linux Images - https://www.kali.org/get-kali/#kali-virtual-machines
- pimpmykali - https://github.com/Dewalt-arch/pimpmykali

# Tmux

Create a new session: tmux new -s Ignite
To list all created sessions: tmux list-session
Create new window: Prefix (ctrl-a) +c
Rename window: Prefix (ctrl-a) + ,
To display summary: Prefix (ctrl-a) + w
Kill the current window: Prefix (ctrl-a) + &

We can now use the following key-binds in TMUX.

Remember Prefix => [CTRL + A]

Prefix + c (Open a new Window)
Prefix + “ (Open a horizontal pane)
Prefix + % (Open a vertical pane)
Prefix + , (Rename the Window)
Mouse Click into any Window or Pane to switch to it.
Prefix + [ (Scrollback mode)

- Click space key to start copying.
- Click Enter key to save the copied text to TMUX buffer.
- Click Prefix + ] to paste the recently copied text from TMUX buffer.
- Click Prefix + = to view the entire TMUX buffer.
- Click Prefix + ? to search above in the terminal output.
- Click Prefix + / to search below in the terminal output
  Prefix + SHIFT + p (Start/Stop logging in the current pane)
  Prefix + ALT + p (Save visible output on the current pane)
  Prefix + ALT + SHIFT + P (Save all the output on the current pane)

## Tmux Plugin Manager

---

git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm

Put this at the bottom of ~/.tmux.conf:

```
# List of plugins
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-sensible'

# Other examples:
# set -g @plugin 'github_username/plugin_name'
# set -g @plugin 'github_username/plugin_name#branch'
# set -g @plugin 'git@github.com:user/plugin'
# set -g @plugin 'git@bitbucket.com:user/plugin'

# Initialize TMUX plugin manager (keep this line at the very bottom of tmux.conf)
run '~/.tmux/plugins/tpm/tpm'
```

Reload TMUX environment so TPM is sourced:

```
# type this in terminal if tmux is already running
tmux source ~/.tmux.conf
```

TMUX Config

```
set -g prefix C-a
bind C-a send-prefix
unbind C-b

set -g history-limit 9999999
set -g mouse on
unbind -n MouseDrag1Pane

set-window-option -g mode-keys vi

set -g @plugin 'tmux-plugins/'
set -g @plugin 'tmux-plugins/sensible'
set -g @plugin 'tmux-plugins/tmux-logging'

set -g @logging-path '#{pane_current_path}'
set -g @save-complete-history-path '#{pane_current_path}'
set -g @screen-capture-path '#{pane_current_path}'

run '~/.tmux/plugins/tpm/tpm'
```

---

TEMP ->

- there’s a config for you to enable mouse mode to scroll in tmux. "set -g mouse on" in your ~/.tmux.config file
- enable logging

  if [[-z $SCRIPT]]; then
  read -p "Enter the Log Script Name: " filename
  export SCRIPT=/opt/bash_log/$(date +%Y%m%d-%H%M%S).$filename.log
  script "$SCRIPT"
  fi

- start tmux with indexing 1

enabling Sudo
which tmux
visudo

Add following line under User Privilege specification =>
test All=(root) NOPASSWD: /usr/bin/tmux
