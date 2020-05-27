test-rerun: Save and Re-run Command Lines
=========================================

XXX better names?
- rerunner
- trerun
- "local history", "contextual history"

__test-rerun__ is a tool for keeping track of previously used command
lines, typically ones used to run the tests in a software project) and
making it easy to re-run them. While superficially similar to command
line history in a shell, it's optimized for keeping track of only
specific command lines, rather than all commands. See the [Related
Work](#related-work) section below for more details on the differences
and motivation for this program.

The test-rerun command itself (actually a shell alias) is `t`. For
each working directory (CWD) in which it is used it keeps a history of
the command lines it has run and optionally allows assigning names to
specific command lines and executing them by name. Named command lines
may also be shared amongst multiple checkouts and users.

XXX explain why `-c COMMAND` is separate; the `t` replaces the command.

### Usage

* `--`: End of arguments to `t`; subsequent arguments are passed to
  the most recently used command line.
* `-l`: List recent saved command lines. Optional argument is the
  number of command lines to list.
* `-c COMMAND`: Path to command to run (more properly, the first item
  on the command line). Default `./Test`.
* `-s NAME`, `-S NAME`: Save the command line for this run under the
  given name. `-s` will not and `-S` will overwrite an existing named
  command line.
* `NAME`: Re-run the command line saved under _name_. Any additional
  arguments given after _name_ will be appended to the command line
  (and saved as the default for the next run). If you wish to pass an
  existing _name_ as an argument, ensure you have a `--` before it
  in the command line.


Configuration and History Files
-------------------------------

The following directories are used for storing configuration and history:
- Config directory: `${XGD_CONFIG_DIR-$HOME/.config}/test-rerun/`
- Cache directory: `${XGD_CACHE_DIR-$HOME/.cache}/test-rerun/`

History files are saved under the cache directory in a separate file
for each CWD. These are named with a hash of the CWD in which they're
used; the path is also stored in the file itself in human-readable
form.

Named command lines are stored in one of three files. In order of
precedence they are:
- Local (to CWD) names, stored in a file (separate from the history
  file) in the cache dir.
- User shared names, stored in a file in the config directory. This is
  enabled by assigning a shared name (XXX fix that, default turned
  off, default name when turned on is basename of git remote, ...).
  These may be shared amongst multiple hosts using [dot-home] or a
  similar mechanism.
- Project shared names, stored in `.test-rerun` in the CWD. These are
  intended to be committed to the project repo to be shared by all
  developers.

We use JSON (at least for history), since we need proper literal
values and arrays, it's in the Python standard library, and we have
little need for hand editing/viewing of the files.


Todo
----

- Figure out how to set up shell alias; "eval $(t -i/--init)"? Can be
  done in `.bashrc`.
- Ways to save/set environment variables.
- Command-line completion.
- Way to edit previously used arguments.
- C
- Two config files per dir, for shared and local-to-host config?
- Option to print separator (blank lines, line of dashes, etc.) before
  running command, for easier finding of start in scrollback.
- Option to load history from that of another CWD.


Related Work
------------

The most obvious related work is Bash's command line history. `t` is a
similar idea, but different in the following ways:

- It saves history of just an individual command (or commands)
  selected by the user, making review of history just for that command
  easier.
- It allows saving specific command/argument combinations under a
  name.
- It saves history on a per-working-directory basis, so history for
  different projects is not conflated.
- It saves history across different shell sessions, even if this is
  not enabled in the shell itself.

Other possibly related works include:

- [rerun]. "A modular shell automation framework to organize your
  keeper scripts." Oriented to saving user-written scripts themselves
  (not parameters) and archiving them to transfer to other systems.



<!-------------------------------------------------------------------->
[dot-home]: https://github.com/dot-home/_dot-home
[rerun]: https://github.com/rerun/rerun
