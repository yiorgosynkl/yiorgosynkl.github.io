---
layout: post
title: Tmux cheatsheet
cover-img: ["assets/img/posts/code-wide.png"]
---

<!-- # Tmux Cheatsheet -->

Tmux is a window manger for the terminal. 
As developers, we have to write code, build projects, see logs, etc.
A structured, easy way to move around and use different shells is necessary.
And tmux in the go-to solution to that.

Each session can have one ore mulitple windows and each window can be split in one or multiple panes.

## Tmux

```
# start a new tmux session 
tmux new -s  <name>

# see existing session
tmux ls
tmux list-sessions

# killing a session (safe terminaltion)
tmux kill-session -t <name>

# enter existing seesions
tmux a - t <name>
tmux attach - t <name>
tmux attach-session -t <name>


# rename seesion
tmux rename-session -t <old-name> <new-name>

# commands when you are in a session (Control + b is the prefix key for all commands)
Ctrl-b c # open new window in seesion
Ctrl-b , # rename window of session
Ctrl-b n # go to next window
Ctrl-b p # go to previous window
Ctrl-b <num> # go to <num>th window
Ctrl-b % # split pane vertically in two
Ctrl-b " # split pane horizontally in two
Ctrl-b <arrow-keys> # navigating panes
Ctrl-d  # to close a shell (classic command)
Ctrl-b d # to exit seesion
```

## More resources

* [Tmux guide](
https://www.hamvocke.com/blog/a-quick-and-easy-guide-to-tmux/)
* [Other cheatsheet](
https://tmuxcheatsheet.com/)

