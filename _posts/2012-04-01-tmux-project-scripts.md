---
layout: default
title: Tmux Project Scripts
---

Setting Up Project Sessions In Tmux
================================================================================

Every time I sit down to work on a project I open a terminal in the project directory, another for [autotest][1], another for [spork][2], and potentially others for a webserver and a console.

In order to automate this process I create a simple shell script for each process that either creates a new tmux session with tabs for each process, or attaches to an existing tmux session if it exists.

Here's an example script:

{% highlight sh %}
#!/bin/sh
########################################################################
# filename: example-project.sh
# purpose : setup the example-project tmux session
########################################################################
set -u
set -e

SESSION_NAME=example-project
PROJECT_PATH=/Users/micah/code/example-project

LAUNCH=false   # prevent unbound error message if LAUNCH isn't set below

tmux attach -t $SESSION_NAME 2>/dev/null && run_status='OK' || LAUNCH=true
if $LAUNCH; then
  tmux new-session -d -s $SESSION_NAME

  tmux rename-window -t $SESSION_NAME:0 'example'
  tmux send-keys -t $SESSION_NAME:0 "cd $PROJECT_PATH; clear" C-m

  tmux new-window -t $SESSION_NAME:1 -n 'test'
  tmux send-keys -t $SESSION_NAME:1 "cd $PROJECT_PATH; clear" C-m
  tmux send-keys -t $SESSION_NAME:1 "AUTOFEATURE=true bundle exec autotest" C-m

  tmux new-window -t $SESSION_NAME:2 -n 'spork'
  tmux send-keys -t $SESSION_NAME:2 "cd $PROJECT_PATH; clear" C-m
  tmux send-keys -t $SESSION_NAME:2 "bundle exec spork" C-m

  tmux select-window -t $SESSION_NAME:0
  tmux attach -t $SESSION_NAME
fi
{% endhighlight %}

[1]: https://github.com/seattlerb/zentest 
[2]: https://github.com/sporkrb/spork
