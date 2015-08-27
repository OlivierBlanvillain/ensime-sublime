# Sublime ENSIME

[![Join the chat at https://gitter.im/ensime/ensime-sublime](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/ensime/ensime-sublime?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

This project provides integration with ENSIME and Sublime Text editor (supports both Sublime Text 2 and 3).
It has recently moved to the Ensime project and is in the process of being re-enlivened - so watch this space.

## Project status

```

06.07.2015: Better completions and ST 3 Support

More work to make completions work better. We also support now support ST 3, see updated instructions.
Now to make it deployed with Package Control...


29.06.2015: It lives a little more

More stabilisation - slightly simpler startup instructions (it works out the .ensime port file for itself).
Lots more to do...

19.06.2015: It lives 

We now have a version that starts and works (at least for some bits) - you need to follow the 'getting it going' 
steps below carefully, all other startup options will fail right now.

Have fun, come and join the party and hack on Ensime-Sublime

11.06.2015: Project moved to Ensime organisation.

With the kind permission of the original authors this project has been moved under the Ensime umbrella.
It is currently in a non-working state.  Please ignore the below instructions - we are working on bringing 
it up to date and into a workable state as soon as possible - watch this space!

```

This is a beta version. It has been hacked to get it working against an up-to-date Ensime, but many things are 
likely to be broken. Jump to source and error hightlight have been seen to work at least once ;)

Please submit issues to [our tracker](https://github.com/ensime/ensime-sublime/issues/new).

## Getting it going
As mentioned above we are in the process of bringing this project back to life - it works (well we think it does) 
but you have to jump through a bunch of hoops to get it working.

### Setup plugin
1. Install [Sublime Text 2 or 3](http://www.sublimetext.com/2) (if you do not have Sublime Text already installed).
2. Clone this project (let's refer to it as `$PLUGIN`).
For Sublime Text 3 switch to branch `ST3` (`git fetch origin ST3 && git checkout ST3`).
3. Manually install Ensime-Sublime
  1. for Sublime Text 2:
  Symlink ```~/Library/Application Support/Sublime Text 2/Packages/Ensime``` (Mac) or ```/.config/sublime-text-2/Packages/Ensime``` (Linux) to `$PLUGIN`.

  2. for Sublime Text 3:  
  Symlink ```~/Library/Application Support/Sublime Text 3/Packages/Ensime``` (Mac) or ```/.config/sublime-text-3/Packages/Ensime``` (Linux) to `$PLUGIN`.

4. Restart Sublime Text.

In future the plugin will be installed using Package Control (still on the TODO list).

### Prepare project
1. Checkout your project (referred to as `$PROJECT`).
3. Add the Ensime sbt plugin to your user sbt configuration (recommended) or directly to the sbt project itself 
(see [ensime-server wiki](https://github.com/ensime/ensime-emacs/wiki/Quick-Start-Guide#installing-the-ensime-sbt-plugin) for details - ignore the Emacs bits).
3. Run ```sbt gen-ensime``` to create a ```.ensime``` file.

### Configure Ensime-Sublime plugin

1. Open Ensime plugin's configuration at `Preference -> Package Settings -> Ensime -> Settings - User`.
2. Add the below entry to the file:
```
{
	"connect_to_external_server": true,
}
```

### Start server and link in Sublime
1. Run the following command in the terminal (both Linux and Mac) to start an Ensime instance for your project:

```
$ $PLUGIN/serverStart.sh $PROJECT/.ensime
```

Note that Ensime Server requires `grealpath` utility (run e.g. `brew install coreutils` to install it). 

2. In Sublime Text create a [new project](http://sublimetext.userecho.com/topic/50034-project-menu-new-project/) with `$PROJECT`
as a root (to do so: open a new window (`Ctrl+Shift+N` for Windows/Linux and `Cmd+Shift+N` for Mac) and open `$PROJECT` as a root).

3. Open the Sublime command palette (typically bound to `Ctrl+Shift+P` on Windows/Linux and `Cmd+Shift+P` on Mac) and type `Ensime: Startup`.

With luck - if you open a Scala file in your project, you should have error highlighting (on save) and jump to definition working!

## Features

* Supports Scala 2.9-2.11.

* Creates and understands `.ensime` projects (maximum one project per Sublime window,
  if you have a project with multiple subprojects only a single subproject will be available at a time).

* Integrates with SBT to generate Ensime projects from SBT projects and provides
  a command, which runs SBT compilation for the current Ensime project.

* Once your Ensime project is configured (we have a helper for that) and Ensime is run,
  Scala files in that Ensime project benefit from a number of semantic services:

    * On-the-fly typechecking and error highlighting on save. Error messages are displayed
      in the status bar when you click highlighted regions (unfortunately, Sublime Text 2 doesn't
      support programmable tooltips). Moreover, errors can be viewed in a dynamically updated buffer
      displayed with `Tools > Ensime > Commands > Show notes`.

    * Type-aware completions for identifiers (integrates into the built-in mechanism of completions
      in Sublime Text 2, depending on your configuration it might be bound to `Ctrl+Space`/`Cmd+Space` or `Tab`).

    * Type-aware go to definition (implemented by `ensime_go_to_definition` command: bind it yourself
      to your favorite hotkey or use the default `Ctrl+Click` binding on Windows/Linux or `Cmd+Click` on Mac).

* Implements experimental support for debugging. At the moment you can set breakpoints, create launch
  configurations, step through programs in the debugger, inspect program output, navigate stack traces
  and watch values of local variables. Things are far from smooth, but it might be worth a try.

## Additional Sublime Configurations

* By default Ensime customizes mouse bindings. It makes
       `Ctrl+Click`/`Cmd+Click` invoke `Go to Definition` and `Alt+Click` stand for `Inspect Type at Point`.
       If you want to disable these bindings or change them bindings to something else,
       adjust the config at `Preferences > Package Settings > Mousemap - Default`.

* For Windows users, make sure the `Line Endings` setting is set to `Unix`.
       You may do this by going to `View > Line Endings` and selecting `Unix`.

## How to use?

Open the Sublime command palette (typically bound to `Ctrl+Shift+P` on Windows/Linux and `Cmd+Shift+P` on Mac) and type `Ensime: Startup`.

If you don't have an Ensime project, the plugin will guide you through creating it.

If you already have a project, an ENSIME server process will be started in the background,
and the server will initialize a resident instance of the Scala compiler.
After the server is ready, a message will appear in the left-hand corner of the status bar.
It will read either `ENSIME` if the currently opened file belongs to the active Ensime project
or `ensime` if it doesn't. Keep an eye on this message - it's an indicator of things going well.

## Troubleshooting

If you find that some features of Ensime are not working properly (i.e. Go To Definition or Error Highlighting), then check the `Line Endings`
setting in Sublime Text.  On Windows, the line endings is set to `Windows` by default. 
Simply change this setting to `Unix` by going to `View > Line Endings` and selecting `Unix`.

## Contacts

Submit issues on the [tracker](https://github.com/ensime/ensime-sublime/issues) or come find us on the 
[ensime-sublime Gitter channel](https://gitter.im/ensime/ensime-sublime).
