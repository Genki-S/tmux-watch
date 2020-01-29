# tmux-watch

`watch` command, for tmux panes.

## Quick start with example program

If `notify-send` doesn't work in your environment, this example doesn't work.

- Clone this repository
- Add the following config in your .tmux.conf
  ```
  unbind-key w
  bind-key w run-shell -b 'PATH/TO/REPO/tmux-watch #{pane_id} PATH/TO/REPO/examples/tmux-watch-notify-certain-strings' \; display-message 'Watching this pane for interesting strings'
  ```
- Reload .tmux.conf (`tmux source-file /PATH/TO/YOUR/.tmux.conf`)
- Run `echo 'working...'; sleep 30` in your shell in your tmux session
- Press `<tmux-prefix>w` right after executing above command
- After the `sleep` process is done and you get your shell prompt back, you should see desktop notification sent via `notify-send`


## Recommended configuration

```
unbind-key w
bind-key w run-shell -b 'PATH/TO/REPO/tmux-watch #{pane_id} PATH/TO/REPO/examples/tmux-watch-notify-certain-strings' \; display-message 'Watching this pane for interesting strings'
unbind-key W
bind-key W run-shell 'PATH/TO/REPO/tmux-watch-unwatch #{pane_id}' \; display-message 'Unwatched this pane'
```

## Customizing

`tmux-watch` script doesn't do much. It's up to you what you do with it.

You provide program you want to run with `tmux-watch` (just like you do with `watch`). The program passed to `tmux-watch` will receieve tmux pane ID it is watching as `-p PANE_ID`.
For example, when you invoke `tmux-watch` as:

```
tmux-watch %1 prog --your-option value
```
(`%1` is tmux pane ID)

Your program will be run once in 5 seconds like:
```
prog -p %1 --your-option value
```

Do whatever you like within your program using the tmux pane ID passed. See scripts in the examples directory for examples.
