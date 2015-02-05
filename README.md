# vim-conda

This is a [Vim](http://www.vim.org/) plugin to support [Python](https://www.python.org/) development using the [Conda](http://conda.pydata.org/docs/) environment manager.

**This plugin is currently in alpha. Unless you like the bleeding edge you should hold off using this until beta.**

Install
-------

[Vundle](https://github.com/gmarik/Vundle.vim) is the recommended way. Add this to the section in your `vimrc` file where all your plugin statments appear:
```
Plugin 'cjrh/vim-conda'
```

Super-short summary
-------------------
When developing Python with Vim, there are *two* Pythons of interest:

0. The one that executes your code in a shell command, i.e. `:!python %`
0. The (embedded in Vim) one that `jedi-vim` uses to provide code completion.

Conda is concerned with the first one, i.e. the "shell Python".  The second one depends on how you have Vim set up with respect to its own Python scripting support.

This plugin provides a command, `CondaChangeEnv`, that will

0. Change the `$PATH` and `$CONDA_DEFAULT_ENV` environment variables *inside* the Vim process, so that new launched processes will have the same environment as if they were launched from a Conda env.
0. Change the *embedded Python sys.path* inside Vim so that tools like `jedi-vim` will provide code completion for the selected env.

Demo
----

Apologies for my poor screencast skills, but I have only a small window of opportunity to get this done and I'm worried it might be a now-or-never type situation.

![gif screencast of plugin demo](https://github.com/cjrh/vim-conda/blob/master/demo.gif)

Introduction
------------

The Vim editor can be used to develop Python code. One popular workflow is to edit the text of a code module (e.g. a `.py` file), and then execute the code with a shell command, such as 
```
:!python %
```
(where `%` will be expanded to the name of the current file). Which `python` will run? Why, the one in the system path of course! But what happens if there is more than one Python executable in the system path? The *first one* to be found will be the one that runs.

This forms the basis of how *virtual environments* work.  The Conda tool is an environment manager for Python; it also supports package management as part of its feature set, but we are not concerned with that here.  Conda allows the user to create multiple, separate Python installations, and switch between them on the command line. It becomes very easy to test code on multiple Python versions, and manage environments that are completely separated from each other.

`vim-conda` makes it easy to perform **switching** environments right from inside Vim.  Now you never have to leave, so the [>300 upvoted question on StackOverflow on "how to quit"](http://stackoverflow.com/questions/11828270/how-to-exit-the-vim-editor) need no longer concern you ;)

This plugin provides only one single command, `CondaChangeEnv`, which you can map to an unused key. You can call the command like so:
```
:CondaChangeEnv<ENTER>
```
You can map it to a key (e.g. in your `vimrc` file) like so:
```
map <F4> :CondaChangeEnv<CR>
```

In the list of envs, you will see `root` as an option if you had previously changed to a non-root env. Selecting `root` is the same as doing a `deactivate` in the sense that all the changes made previously are rolled back.

Likewise, when you change from one environment to another, the change is clean in the sense that changes from the first env are reset, before changes for the new env are made.  Exactly as would happen on the command line.

Details
-------

The `CondaChangeEnv` command will trigger wildmode allowing you to tab through the existing Conda environments on your system. When an environment is selected, the following happens: 

- A new environment variable called `$CONDA_DEFAULT_ENV` is created *inside the running Vim process*
- The `$PATH` variable is set to be the selected conda env, plus the associated `/Scripts` folder, as per the usual way the `activate` script supplied by Conda would modify the path. Note that the `$PATH` environment variable *inside the running Vim process* is modified.
- The `sys.path` list of the **embedded Python instance** inside the running Vim process is modified to include the entries for the selected Conda env.  This is done so that the Jedi-vim package will automatically be able to perform code completion within the selected env.

