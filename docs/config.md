# Config

The config file used by dotdrop is
[config.yaml](https://github.com/deadc0de6/dotdrop/blob/master/config.yaml).

## Location

Unless specified dotdrop will look in following places for the config file (`config.yaml`)
and use the first one found

* current/working directory or the directory where [dotdrop.sh](https://github.com/deadc0de6/dotdrop/blob/master/dotdrop.sh) is located if used
* `${XDG_CONFIG_HOME}/dotdrop/`
* `~/.config/dotdrop/`
* `/etc/xdg/dotdrop/`
* `/etc/dotdrop/`

You can force dotdrop to use a different file either by using the `-c --cfg` cli switch
or by defining the `DOTDROP_CONFIG` environment variable.

## Format

Dotdrop config file uses [yaml](https://yaml.org/) syntax.

### config entry

The **config** entry (mandatory) contains settings for the deployment

* `backup`: create a backup of the dotfile in case it differs from the
  one that will be installed by dotdrop (default *true*)
* `banner`: display the banner (default *true*)
* `cmpignore`: list of patterns to ignore when comparing, apply to all dotfiles
  (enclose in quotes when using wildcards, see [ignore patterns](#ignore-patterns))
* `create`: create directory hierarchy when installing dotfiles if
  it doesn't exist (default *true*)
* `default_actions`: list of action's keys to execute for all installed dotfile
  (see [actions](#entry-actions))
* `diff_command`: the diff command to use for diffing files (default `diff -r -u {0} {1}`)
* `dotpath`: path to the directory containing the dotfiles to be managed (default `dotfiles`)
  by dotdrop (absolute path or relative to the config file location)
* `filter_file`: list of paths to load templating filters from
  (see [Templating available filters](templating.md#available-filters))
* `func_file`: list of paths to load templating functions from
   (see [Templating available methods](templating.md#available-methods))
* `ignoreempty`: do not deploy template if empty (default *false*)
* `import_actions`: list of paths to load actions from
  (absolute path or relative to the config file location,
  see [Import actions from file](#entry-import_actions))
* `import_configs`: list of config file paths to be imported in
  the current config (absolute path or relative to the current config file location,
  see [Import config files](#entry-import_configs))
* `import_variables`: list of paths to load variables from
  (absolute path or relative to the config file location,
  see [Import variables from file](#entry-import_variables))
* `instignore`: list of patterns to ignore when installing, apply to all dotfiles
  (enclose in quotes when using wildcards, see [ignore patterns](#ignore-patterns))
* `keepdot`: preserve leading dot when importing hidden file in the `dotpath` (default *false*)
* `link_dotfile_default`: set dotfile's `link` attribute to this value when undefined.
  Possible values: *nolink*, *link*, *link_children* (default: *nolink*,
  see [Symlinking dotfiles](#symlink-dotfiles))
* `link_on_import`: set dotfile's `link` attribute to this value when importing.
  Possible values: *nolink*, *link*, *link_children* (default: *nolink*,
  see [Symlinking dotfiles](#symlink-dotfiles))
* `longkey`: use long keys for dotfiles when importing (default *false*,
  see [Import dotfiles](usage.md#import-dotfiles))
* `minversion`: (*for internal use, do not modify*) provides the minimal dotdrop version to use
* `showdiff`: on install show a diff before asking to overwrite (see `--showdiff`) (default *false*)
* `upignore`: list of patterns to ignore when updating, apply to all dotfiles
  (enclose in quotes when using wildcards, see [ignore patterns](#ignore-patterns))
* `workdir`: path to the directory where templates are installed before being symlinked
  when using `link:link` or `link:link_children`
  (absolute path or relative to the config file location, defaults to *~/.config/dotdrop*)
* *DEPRECATED* `link_by_default`: when importing a dotfile set `link` to that value per default (default *false*)

### dotfiles entry

The **dotfiles** entry (mandatory) contains a list of dotfiles managed by dotdrop

* `dst`: where this dotfile needs to be deployed
  (dotfile with empty `dst` are ignored and considered installed,
  can use `variables` and `dynvariables`, make sure to quote)
* `src`: dotfile path within the `dotpath`
  (dotfile with empty `src` are ignored and considered installed,
  can use `variables` and `dynvariables`, make sure to quote)
* `link`: define how this dotfile is installed.
  Possible values: *nolink*, *link*, *link_children* (default: `link_dotfile_default`,
  see [Symlinking dotfiles](#symlink-dotfiles))
* `actions`: list of action keys that need to be defined in the **actions** entry below
  (see [actions](#entry-actions))
* `cmpignore`: list of patterns to ignore when comparing (enclose in quotes when using wildcards,
  see [ignore patterns](#ignore-patterns))
* `ignoreempty`: if true empty template will not be deployed (defaults to the value of `ignoreempty` above)
* `instignore`: list of patterns to ignore when installing (enclose in quotes when using wildcards,
  see [ignore patterns](#ignore-patterns))
* `trans_read`: transformation key to apply when installing this dotfile
  (must be defined in the **trans_read** entry below, see [transformations](#entry-transformations))
* `trans_write`: transformation key to apply when updating this dotfile
  (must be defined in the **trans_write** entry below, see [transformations](#entry-transformations))
* `upignore`: list of patterns to ignore when updating (enclose in quotes when using wildcards,
  see [ignore patterns](#ignore-patterns))
* *DEPRECATED* `link_children`: replaced by `link: link_children`
* *DEPRECATED* `trans`: replaced by `trans_read`

```yaml
<dotfile-key-name>:
  dst: <where-this-file-is-deployed>
  src: <filename-within-the-dotpath>
  ## Optional
  link: (nolink|link|link_children)
  ignoreempty: (true|false)
  cmpignore:
    - "<ignore-pattern>"
  upignore:
    - "<ignore-pattern>"
  instignore:
    - "<ignore-pattern>"
  actions:
    - <action-key>
  trans_read: <transformation-key>
  trans_write: <transformation-key>
```

### profiles entry

The **profiles** entry (mandatory) contains a list of profiles with the different dotfiles that
  need to be managed

* `dotfiles`: the dotfiles associated to this profile
* `import`: list of paths containing dotfiles keys for this profile
  (absolute path or relative to the config file location,
  see [Import profile dotfiles from file](#entry-profile-import)).
* `include`: include all elements (dotfiles, actions, (dyn)variables, etc) from another profile
  (see [Include dotfiles from another profile](#entry-profile-include))
* `variables`: profile specific variables
  (see [Variables](#variables))
* `dynvariables`: profile specific interpreted variables
  (see [Interpreted variables](#entry-dynvariables))
* `actions`: list of action keys that need to be defined in the **actions** entry below
  (see [actions](#entry-actions))

```yaml
<some-profile-name-usually-the-hostname>:
  dotfiles:
  - <some-dotfile-key-name-defined-above>
  - <some-other-dotfile-key-name>
  - ...
  ## Optional
  include:
  - <some-other-profile>
  - ...
  variables:
    <name>: <value>
  dynvariables:
    <name>: <value>
  actions:
  - <some-action>
  - ...
  import:
  - <some-path>
  - ...
```

### actions entry

The **actions** entry (optional) contains a list of actions (see [actions](#entry-actions))

```yaml
actions:
  <action-key>: <command-to-execute>
```

### trans_read entry

The **trans_read** entry (optional) contains a list of transformations (see [transformations](#entry-transformations))

```yaml
trans_read:
   <trans-key>: <command-to-execute>
```

### trans_write entry

The **trans_write** entry (optional) contains a list of write transformations (see [transformations](#entry-transformations))

```yaml
trans_write:
   <trans-key>: <command-to-execute>
```

### variables entry

The **variables** entry (optional) contains a list of variables (see [variables](#variables))

```yaml
variables:
  <variable-name>: <variable-content>
```

### dynvariables entry

The **dynvariables** entry (optional) contains a list of interpreted variables
(see [Interpreted variables](#entry-dynvariables))

```yaml
dynvariables:
  <variable-name>: <shell-oneliner>
```

## Variables

Multiple variables can be used within the config file to
parametrize following elements of the config:

* dotfiles `src` and `dst` paths (see [Dynamic dotfile paths](#dynamic-dotfile-paths))
* external path specifications
  * `import_variables`
  * `import_actions`
  * `import_configs`
  * profiles's `import`

`actions` and `transformations` also support the use of variables
but those are resolved when the action/transformation is executed
(see [Dynamic actions](#dynamic-actions),
[Dynamic transformations](#dynamic-transformations) and [Templating](templating.md)).

Following variables are available in the config files:

* [variables defined in the config](#entry-variables)
* [interpreted variables defined in the config](#entry-dynvariables)
* [profile variables defined in the config](#profile-variables)
* environment variables: `{{@@ env['MY_VAR'] @@}}`
* dotdrop header: `{{@@ header() @@}}` (see [Dotdrop header](templating.md#dotdrop-header))

As well as all template methods (see [Available methods](templating.md#available-methods))

## Symlink dotfiles

Dotdrop is able to install dotfiles in three different ways
which are controlled by the `link` config attribute of each dotfile:

* `link: nolink`: the dotfile (file or directory) is copied to its destination
* `link: link`: the dotfile (file or directory) is symlinked to its destination
* `link: link_children`: the files/directories found under the dotfile (directory) are symlinked to their destination

For more see [this how-to](howto/symlink-dotfiles.md)

## Entry actions

**Note**: any action with a key starting with an underscore (`_`) won't be shown in output.
This can be useful when working with sensitive data containing passwords for example.

### Dotfile actions

It is sometimes useful to execute some kind of action
when deploying a dotfile.

Note that dotfile actions are only
executed when the dotfile is installed.

For example let's consider
[Vundle](https://github.com/VundleVim/Vundle.vim) is used
to manage vim's plugins, the following action could
be set to update and install the plugins when `vimrc` is
deployed:

```yaml
actions:
  vundle: vim +VundleClean! +VundleInstall +VundleInstall! +qall
config:
  backup: true
  create: true
  dotpath: dotfiles
dotfiles:
  f_vimrc:
    dst: ~/.vimrc
    src: vimrc
    actions:
      - vundle
profiles:
  home:
    dotfiles:
    - f_vimrc
```

Thus when `f_vimrc` is installed, the command
`vim +VundleClean! +VundleInstall +VundleInstall! +qall` will
be executed.

Sometimes, you may even want to execute some action prior to deploying a dotfile.
Let's take another example with
[vim-plug](https://github.com/junegunn/vim-plug):

```yaml
actions:
  pre:
    vim-plug-install: test -e ~/.vim/autoload/plug.vim || (mkdir -p ~/.vim/autoload; curl
      -fLo ~/.vim/autoload/plug.vim https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim)
  vim-plug: vim +PlugInstall +qall
config:
  backup: true
  create: true
  dotpath: dotfiles
dotfiles:
  f_vimrc:
    dst: ~/.vimrc
    src: vimrc
    actions:
       - vim-plug-install
       - vim-plug
profiles:
  home:
    dotfiles:
    - f_vimrc
```

This way, we make sure [vim-plug](https://github.com/junegunn/vim-plug)
is installed prior to deploying the `~/.vimrc` dotfile.

You can also define `post` actions like this:

```yaml
actions:
  post:
    some-action: echo "Hello, World!" >/tmp/log
```

If you don't specify neither `post` nor `pre`, the action will be executed
after the dotfile deployment (which is equivalent to `post`).
Actions cannot obviously be named `pre` or `post`.

Actions can even be parameterized. For example:
```yaml
actions:
  echoaction: echo '{0}' > {1}
config:
  backup: true
  create: true
  dotpath: dotfiles
dotfiles:
  f_vimrc:
    dst: ~/.vimrc
    src: vimrc
    actions:
      - echoaction "vim installed" /tmp/mydotdrop.log
  f_xinitrc:
    dst: ~/.xinitrc
    src: xinitrc
    actions:
      - echoaction "xinitrc installed" /tmp/myotherlog.log
profiles:
  home:
    dotfiles:
    - f_vimrc
    - f_xinitrc
```

The above will execute `echo 'vim installed' > /tmp/mydotdrop.log` when
vimrc is installed and `echo 'xinitrc installed' > /tmp/myotherlog.log'`
when xinitrc is installed.

### Default actions

Dotdrop allows to execute an action for any dotfile installation. These actions work as any other action (*pre* or *post*).

For example, the below action will log each dotfile installation to a file.

```yaml
actions:
  post:
    loginstall: "echo {{@@ _dotfile_abs_src @@}} installed to {{@@ _dotfile_abs_dst @@}} >> {0}"
config:
  backup: true
  create: true
  dotpath: dotfiles
  default_actions:
  - loginstall "/tmp/dotdrop-installation.log"
dotfiles:
  f_vimrc:
    dst: ~/.vimrc
    src: vimrc
profiles:
  hostname:
    dotfiles:
    - f_vimrc
```

### Profile actions

Profile can be either `pre` or `post` actions. Those are executed before
any dotfile installation (for `pre`) and after all dotfiles installation (for `post`)
only if at least one dotfile has been installed.

### Fake dotfile and actions

*Fake* dotfile can be created by specifying no `dst` and no `src` (see [config format](#format)).
By binding an action to such a *fake* dotfile, you make sure the action is always executed since
*fake* dotfile are always considered installed.

```yaml
actions:
  always_action: 'date > ~/.dotdrop.log'
dotfiles:
  fake:
    src:
    dst:
    actions:
    - always_action
```

## Entry transformations

For examples of transformation uses, see

* [Store compressed directories](howto/store-compressed-directories.md)
* [Sensitive dotfiles](howto/sensitive-dotfiles.md)

**Note**: any transformation with a key starting with an underscore (`_`) won't be shown in output.
This can be useful when working with sensitive data containing passwords for example.

There are two types of transformations available:

* **read transformations**: used to transform dotfiles before they are installed ([format](#format) key `trans_read`)
    * Used for commands `install` and `compare`
    * They have two arguments:
        * **{0}** will be replaced with the dotfile to process
        * **{1}** will be replaced with a temporary file to store the result of the transformation
    * Happens **before** the dotfile is templated with jinja2 (see [templating](templating.md))

* **write transformations**: used to transform files before updating a dotfile ([format](#format) key `trans_write`)
    * Used for command `update`
    * They have two arguments:
        * **{0}** will be replaced with the file path to update the dotfile with
        * **{1}** will be replaced with a temporary file to store the result of the transformation

A typical use-case for transformations is when dotfiles need to be
stored encrypted or compressed. For more see [the howto](howto/README.md).

Note that transformations cannot be used if the dotfiles is to be linked (when `link: link` or `link: link_children`).

Transformations also support additional positional arguments that must start from 2 (since `{0}` and `{1}` are added automatically). The transformations itself as well as its arguments can also be templated.

For example
```yaml
trans_read:
  targ: echo "$(basename {0}); {{@@ _dotfile_key @@}}; {2}; {3}" > {1}
dotfiles:
  f_abc:
    dst: /tmp/abc
    src: abc
    trans_read: targ "{{@@ profile @@}}" lastarg
profiles:
  p1:
    dotfiles:
    - f_abc
```

will result in `abc; f_abc; p1; lastarg`

## Entry variables

Variables defined in the `variables` entry are made available within the config file.

Config variables are recursively evaluated what means that
a config like the below
```yaml
variables:
  var1: "var1"
  var2: "{{@@ var1 @@}} var2"
  var3: "{{@@ var2 @@}} var3"
  var4: "{{@@ dvar4 @@}}"
dynvariables:
  dvar1: "echo dvar1"
  dvar2: "{{@@ dvar1 @@}} dvar2"
  dvar3: "{{@@ dvar2 @@}} dvar3"
  dvar4: "echo {{@@ var3 @@}}"
```

will result in the following available variables:

* var1: `var1`
* var2: `var1 var2`
* var3: `var1 var2 var3`
* var4: `echo var1 var2 var3`
* dvar1: `dvar1`
* dvar2: `dvar1 dvar2`
* dvar3: `dvar1 dvar2 dvar3`
* dvar4: `var1 var2 var3`

### Profile variables

Profile variables will take precedence over globally defined variables.
This means that you could do something like this:
```yaml
variables:
  git_email: home@email.com
dotfiles:
  f_gitconfig:
    dst: ~/.gitconfig
    src: gitconfig
profiles:
  work:
    dotfiles:
    - f_gitconfig
    variables:
      git_email: work@email.com
  private:
    dotfiles:
    - f_gitconfig
```

## Entry dynvariables

It is also possible to have *dynamic* variables in the sense that their
content will be interpreted by the shell before being substituted.

These need to be defined in the config file under the entry `dynvariables`.

For example:
```yaml
dynvariables:
  dvar1: head -1 /proc/meminfo
  dvar2: "echo 'this is some test' | rev | tr ' ' ','"
  dvar3: /tmp/my_shell_script.sh
  user: "echo $USER"
  config_file: test -f "{{@@ user_config @@}}" && echo "{{@@ user_config @@}}" || echo "{{@@ dfl_config @@}}"
variables:
  user_config: "profile_{{@@ user @@}}_uid.yaml"
  dfl_config: "profile_default.yaml"
```

They have the same properties as [Variables](#variables).

## Entry profile include

If one profile is using the entire set of another profile, one can use
the `include` entry to avoid redundancy.

Note that everything from the included profile is made available
(actions, variables/dynvariables, etc).

For example:
```yaml
profiles:
  host1:
    dotfiles:
      - f_xinitrc
    include:
      - host2
  host2:
    dotfiles:
      - f_vimrc
```
Here profile *host1* contains all the dotfiles defined for *host2* plus `f_xinitrc`.

For more advanced use-cases variables
([variables](#variables) and [dynvariables](#entry-dynvariables))
can be used to specify the profile to include in a profile

For example:
```yaml
variables:
  var1: "john"
dynvariables:
  d_user: "echo $USER"
profiles:
  profile_john:
    dotfiles:
    - f_john_dotfile
  profile_bill:
    dotfiles:
    - f_bill_dotfile
  p1:
    include:
    - "profile_{{@@ d_user @@}}"
  p2:
    include:
    - "profile_{{@@ var1 @@}}"
```

## Entry profile import

Profile's dotfiles list can be loaded from external files
by specifying their paths in the config entry `import` under the specific profile.

The paths can be absolute or relative to the config file location.

`config.yaml`
```yaml
dotfiles:
  f_abc:
    dst: ~/.abc
    src: abc
  f_def:
    dst: ~/.def
    src: def
  f_xyz:
    dst: ~/.xyz
    src: xyz
profiles:
  p1:
    dotfiles:
    - f_abc
    import:
    - somedotfiles.yaml
```

`somedotfiles.yaml`
```
dotfiles:
  - f_def
  - f_xyz
```

Variables can be used in `import` and would allow to do something like
```yaml
import:
- profiles.d/{{@@ profile @@}}.yaml
```

## Entry import_variables

It is possible to load variables/dynvariables from external files by providing their
paths in the config entry `import_variables`.

The paths can be absolute or relative to the config file location.

`config.yaml`
```yaml
config:
  backup: true
  create: true
  dotpath: dotfiles
  import_variables:
  - variables.d/myvars.yaml
```

`variables.d/myvars.yaml`
```yaml
variables:
  var1: "extvar1"
dynvariables:
  dvar1: "echo extdvar1"
```

Dotdrop will fail if an imported path points to a non-existing file.
It is possible to make non-existing paths not fatal by appending the path with `:optional`
```yaml
import_variables:
- variables.d/myvars.yaml:optional
```

## Entry import_actions

It is possible to load actions from external files by providing their
paths in the config entry `import_actions`.

The paths can be absolute or relative to the config file location.

`config.yaml`
```yaml
config:
  backup: true
  create: true
  dotpath: dotfiles
  import_actions:
  - actions.d/myactions.yaml
dotfiles:
  f_abc:
    dst: ~/.abc
    src: abc
    actions:
      - dateme
```

`actions.d/myactions.yaml`
```yaml
actions:
  dateme: date > /tmp/timestamp
```

External variables will take precedence over variables defined within
the source config file.

Dotdrop will fail if an imported path points to a non-existing file.
It is possible to make non-existing paths not fatal by appending the path with `:optional`
```yaml
import_actions:
- actions.d/myactions.yaml:optional
```

## Entry import_configs

Entire config files can be imported using the `import_configs` entry.
This means making the following available from the imported config file in the original config file:

* dotfiles
* profiles
* actions
* read/write transformations
* variables/dynvariables

Paths to import can be absolute or relative to the importing config file
location.

`config.yaml`
```yaml
config:
  backup: true
  create: true
  dotpath: dotfiles
  import_configs:
  - other-config.yaml
dotfiles:
  f_abc:
    dst: ~/.abc
    src: abc
    actions:
    - show
profiles:
  my-host:
    dotfiles:
    - f_abc
    - f_def
  my-haskell:
    include:
    - other-host
```

`other-config.yaml`
```yaml
config:
  backup: true
  create: true
  dotpath: dotfiles-other
  import_actions:
  - actions.yaml
dotfiles:
  f_def:
    dst: ~/.def
    src: def
  f_ghci:
    dst: ~/.ghci
    src: ghci
profiles:
  other-host:
    dotfiles:
    - f_gchi
```

`actions.yaml`
```yaml
actions:
  post:
    show: less
```

In this example `config.yaml` imports `other-config.yaml`. The dotfile `f_def`
used in the profile `my-host` is defined in `other-config.yaml`, and so is the
profile `other-host` included from `my-haskell`. The action `show` is defined
in `actions.yaml`, which is in turn imported by `other-config.yaml`.

Dotdrop will fail if an imported path points to a non-existing file.
It is possible to make non-existing paths not fatal by appending the path with `:optional`
```yaml
import_configs:
- other-config.yaml:optional
```

## Dynamic dotfile paths

Dotfile source (`src`) and destination (`dst`) can be dynamically constructed using
defined variables ([variables and dynvariables](#variables)).

For example to have a dotfile deployed on the unique firefox profile where the
profile path is dynamically found using a shell oneliner stored in a dynvariable:
```yaml
dynvariables:
  mozpath: find ~/.mozilla/firefox -name '*.default'
dotfiles:
  f_somefile:
    dst: "{{@@ mozpath @@}}/somefile"
    src: firefox/somefile
profiles:
  home:
    dotfiles:
    - f_somefile
```

Make sure to quote the path in the config file.

## Dynamic actions

Variables ([config variables and dynvariables](#variables)
and [template variables](templating.md#template-variables)) can be used
in actions for more advanced use-cases.

```yaml
dotfiles:
  f_test:
    dst: ~/.test
    src: test
    actions:
      - cookie_mv_somewhere "/tmp/moved-cookie"
variables:
  cookie_dir_available: (test -d /tmp/cookiedir || mkdir -p /tmp/cookiedir)
  cookie_header: "{{@@ cookie_dir_available @@}} && echo 'header' > /tmp/cookiedir/cookie"
  cookie_mv: "{{@@ cookie_header @@}} && mv /tmp/cookiedir/cookie"
actions:
  cookie_mv_somewhere: "{{@@ cookie_mv @@}} {0}"
```

or even something like this:
```yaml
actions:
  log: "echo {0} >> {1}"
config:
  default_actions:
  - preaction '{{@@ _dotfile_key @@}} installed' "/tmp/log"
...
```

Make sure to quote the actions using variables.

## Dynamic transformations

As for [dynamic actions](#dynamic-actions), transformations support
the use of variables ([variables and dynvariables](#variables)
and [template variables](templating.md#template-variables)).

A very dumb example:
```yaml
trans_read:
  r_echo_abs_src: echo "{0}: {{@@ _dotfile_abs_src @@}}" > {1}
  r_echo_var: echo "{0}: {{@@ r_var @@}}" > {1}
trans_write:
  w_echo_key: echo "{0}: {{@@ _dotfile_key @@}}" > {1}
  w_echo_var: echo "{0}: {{@@ w_var @@}}" > {1}
variables:
  r_var: readvar
  w_var: writevar
dotfiles:
  f_abc:
    dst: ${tmpd}/abc
    src: abc
    trans_read: r_echo_abs_src
    trans_write: w_echo_key
  f_def:
    dst: ${tmpd}/def
    src: def
    trans_read: r_echo_var
    trans_write: w_echo_var
```

## All dotfiles for a profile

To use all defined dotfiles for a profile, simply use
the keyword `ALL`.

For example:
```yaml
dotfiles:
  f_xinitrc:
    dst: ~/.xinitrc
    src: xinitrc
  f_vimrc:
    dst: ~/.vimrc
    src: vimrc
profiles:
  host1:
    dotfiles:
    - ALL
  host2:
    dotfiles:
    - f_vimrc
```


## Ignore patterns

It is possible to ignore specific patterns when using dotdrop. For example for `compare` when temporary
files don't need to appear in the output.

* for [install](usage.md#install-dotfiles)
    * using `instignore` in the config file
* for [compare](usage.md#compare-dotfiles)
    * using `cmpignore` in the config file
    * using the command line switch `-i --ignore`
* for [update](usage.md#update-dotfiles)
    * using `upignore` in the config file
    * using the command line switch `-i --ignore`

The ignore pattern must follow Unix shell-style wildcards like for example `*/path/to/file`.
Make sure to quote those when using wildcards in the config file.

Patterns used on a specific dotfile can be specified relative to the dotfile destination (`dst`).

```yaml
config:
  cmpignore:
  - '*/README.md'
  upignore:
  - '*/README.md'
  instignore:
  - '*/README.md'
...
dotfiles:
  d_vim
    dst: ~/.vim
    src: vim
    upignore:
    - "*/undo-dir"
    - "*/plugged"
...
```

To completely ignore comparison of a specific dotfile:
```yaml
dotfiles:
  d_vim
    dst: ~/.vim
    src: vim
    cmpignore:
    - "*"
```

To ignore specific directory when updating
```yaml
dotfiles:
  d_colorpicker:
    src: config/some_directory
    dst: ~/.config/some_directory
    upignore:
      - '*sub_directory_to_ignore'
```
