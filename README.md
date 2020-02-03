# tmux-watch

`watch` command, for tmux panes.

## Motivation

So many times, I fire long-running process and leave computer, and when I get
back I'm faced with timed out `[sudo] password for USER:` prompt and failed
process. This is not right, the process (or something which can monitor that
process) should send me push notification if it needs my attention.

tmux-watch can be used to solve this problem, and potentially more problems I don't yet know.

## What it does

`tmux-watch` script doesn't do much. It's up to you what you do with it.

You provide program you want to run with `tmux-watch` (just like you do with
`watch`). The program passed to `tmux-watch` will receieve tmux pane ID it is
watching as `-p PANE_ID`. For example, when you invoke `tmux-watch` as:

```
tmux-watch %1 prog --your-option value
```
(`%1` is tmux pane ID)

Your program will be run once in 5 seconds like:
```
prog -p %1 --your-option value
```

Do whatever you like within your program using the tmux pane ID passed. See scripts in the examples directory for examples.

Your program can communicate to `tmux-watch` (the parent process) using exit status as follows:
- exit with 0 ("continue watching"): `tmux-watch` continues to run your program periodically
- exit with 42 ("stop watching"): `tmux-watch` will exit with status 0
- exit with other status ("something went wrong"): `tmux-watch` will exit with status returned from your program

The scripts in this repository are fairly small. Please read the scripts to learn the exact behavior.
`man tmux` is also your friend.

## Difference with `watch`

tmux-watch provides following features, which cannot be achieved with pure `watch`:

- Ensures there's at most 1 tmux-watch process per tmux pane
- Allows the passed program to stop tmux-watch process gracefully
  - when program exit with status 42, tmux-watch process will terminate with exit code 0
  - this is useful to, for example, prevent notifications from firing multiple times
- Allows un-watching the already watched pane

## Quick start with example program

If `notify-send` doesn't work in your environment, this example doesn't work.

- Clone this repository
- Add the following config in your .tmux.conf
  ```
  unbind-key w
  bind-key w run-shell -b 'PATH/TO/REPO/tmux-watch #{pane_id} PATH/TO/REPO/examples/tmux-watch-notify-certain-strings' \; display-message 'Watching this pane for interesting strings'
  ```
- Reload .tmux.conf (`tmux source-file /PATH/TO/YOUR/.tmux.conf`)
- Run `echo 'working...'; sleep 30; sudo ls` in your shell in your tmux session (make sure you don't have sudo session)
- Press `<tmux-prefix>w` right after executing above command
- When `sudo ls` in the above command shows sudo prompt, you should see desktop notification sent via `notify-send`


## Recommended configuration

```
unbind-key w
bind-key w run-shell -b 'PATH/TO/REPO/tmux-watch #{pane_id} PATH/TO/REPO/examples/tmux-watch-notify-certain-strings' \; display-message 'Watching this pane for interesting strings'
unbind-key W
bind-key W run-shell 'PATH/TO/REPO/tmux-watch-unwatch #{pane_id}' \; display-message 'Unwatched this pane'
```
