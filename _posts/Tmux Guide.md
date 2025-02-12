SRC : https://stackoverflow.com/questions/5609192/how-to-set-up-tmux-so-that-it-starts-up-with-specified-windows-opened

Example
```
alias tmuxcluster="tmux new-session  \; send-keys 'ssh n1.bd' C-m \; split-window -h \; send-keys 'ssh n2.bd' C-m \; split-window -v \; send-keys 'ssh n4.bd' C-m \; select-pane -t 0 \; split-window -v \; send-keys 'ssh n3.bd' C-m \; setw synchronize-panes \; select-pane -t 0"
```
tmux new-session \; 

tmux new-session \;
Gets you started with a new session. To split it horizontal or vertical use split-window -h or -v subsequently, like that:

tmux new-session \; split-window -v \; split-window -h \;
Creates 3 panes, like this:

------------
|          |
|----------|
|    |     |
------------
To run commands in that panes, just add them with the send-keys 'my-command' command and C-m which executes it:

tmux new-session \; \
  send-keys 'tail -f /var/log/monitor.log' C-m \; \
  split-window -v \; \
  split-window -h \; \
  send-keys 'top' C-m \; 
And the resulting session should look like that.

------------
|  tail    |
|----------|
|    | top |
------------
Now I tried to again sub-divide the bottom left pane, so switching either back using last-pane, or in more complex windows, with the select-pane -t 1 where 1 is the number of the pane in order created starting with 0.

tmux new-session \; \
  send-keys 'tail -f /var/log/monitor.log' C-m \; \
  split-window -v \; \
  split-window -h \; \
  send-keys 'top' C-m \; \
  select-pane -t 1 \; \
  split-window -v \; \
  send-keys 'weechat' C-m \;
Does that. Basicaly knowing your way around with split-window and select-pane is all you need. It's also handy to pass with -p 75 a percentage size of the pane created by split-window to have more control over the size of the panes.

tmux new-session \; \
  send-keys 'tail -f /var/log/monitor.log' C-m \; \
  split-window -v -p 75 \; \
  split-window -h -p 30 \; \
  send-keys 'top' C-m \; \
  select-pane -t 1 \; \
  split-window -v \; \
  send-keys 'weechat' C-m \;
Which results in a session looking like that

------------------
|      tail      |
|----------------|
|          | top |
|----------|     |
| weechat  |     |
------------------
Hope that helps tmux enthusiasts in the future.
