# FAQ

## How do I update my local packages?
First update the formulae and Homebrew itself:

    brew update

You can now find out what is outdated with:

    brew outdated

Upgrade everything with:

    brew upgrade

Or upgrade a specific formula with:

    brew upgrade <formula>

## How do I stop certain formulae from being updated?
To stop something from being updated/upgraded:

    brew pin <formula>

To allow that formulae to update again:

    brew unpin <formula>

Note that pinned, outdated formulae that another formula depends on need to be upgraded when required as we do not allow formulae to be built against non-latest versions.

## How do I uninstall old versions of a formula?
By default, Homebrew does not uninstall old versions of a formula, so
over time you will accumulate old versions. To remove them, simply use:

    brew cleanup <formula>

or clean up everything at once:

    brew cleanup

or to see what would be cleaned up:

    brew cleanup -n

## How do I uninstall Homebrew?
To uninstall Homebrew, paste the command below in a terminal prompt.

```sh
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/a1003584293/Linuxbrew-install/master/uninstall)"
```
Download the [uninstall script](https://raw.githubusercontent.com/a1003584293/Linuxbrew-install/master/uninstall)
and run `./uninstall --help` to view more uninstall options.

## How do I uninstall a formula?
If you do not uninstall all of the versions that Homebrew has installed,
Homebrew will continue to attempt to install the newest version it knows
about when you run `brew upgrade`. This can be surprising.

To remove a formula entirely, you may run `brew uninstall --force <formula>`.

Be careful as this is a destructive operation.

## Where does stuff get downloaded?

    brew --cache

Which is usually: `~/Library/Caches/Homebrew`

## My Mac `.app`s don’t find `/usr/local/bin` utilities!
GUI apps on macOS don’t have `/usr/local/bin` in their `PATH` by
default. If you’re on Mountain Lion or later, you can fix this by
running `sudo launchctl config user path "/usr/local/bin:$PATH"` and
then rebooting, as documented in `man launchctl`. Note that this sets
the launchctl PATH for *all users*. For earlier versions of macOS, see
[this page](https://developer.apple.com/legacy/library/qa/qa1067/_index.html).

## How do I contribute to Homebrew?
Read our [contribution guidelines](https://github.com/Homebrew/brew/blob/master/CONTRIBUTING.md#contributing-to-homebrew).

## Why do you compile everything?
Homebrew provides pre-compiled versions for many formulae. These
pre-compiled versions are referred to as [bottles](Bottles.md) and are available
at <https://bintray.com/homebrew/bottles>.

If available, bottled binaries will be used by default except under the
following conditions:

* Options were passed to the install command, i.e. `brew install <formula>`
will use a bottled version of the formula, but
`brew install --enable-bar <formula>` will trigger a source build.
* The `--build-from-source` option is invoked.
* The environment variable `HOMEBREW_BUILD_FROM_SOURCE` is set
(intended for developers only).
* The machine is not running a supported version of macOS as all
bottled builds are generated only for supported macOS versions.
* Homebrew is installed to a prefix other than the standard
`/usr/local` (although some bottles support this).

We aim to bottle everything.

## How do I get a formula from someone else’s branch?

```sh
brew install hub
brew update
cd $(brew --repository)
hub pull someone_else
```

Or:

    brew pull https://github.com/Homebrew/homebrew-core/pull/1234

## Why does Homebrew prefer I install to `/usr/local`?

1.  **It’s easier**<br>`/usr/local/bin` is already in your
    `PATH`.
2.  **It’s easier**<br>Tons of build scripts break if their dependencies
    aren’t in either `/usr` or `/usr/local`. We
    fix this for Homebrew formulae (although we don’t always test for
    it), but you’ll find that many RubyGems and Python setup scripts
    break which is something outside our control.
3.  **It’s safe**<br>Apple has assigned this directory for non-system utilities. This means
    there are no files in `/usr/local` by default, so there
    is no need to worry about messing up existing or system tools.

**If you plan to install gems that depend on
brews then save yourself a bunch of hassle and install to
`/usr/local`!**

It is not always straightforward to tell `gem` to look in non-standard directories for headers and libraries. If you choose `/usr/local`, many things will "just work".

## Why does Homebrew say sudo is bad?
**tl;dr** Sudo is dangerous, and you installed TextMate.app without sudo
anyway.

Homebrew refuses to work using sudo.

You should only ever sudo a tool you trust. Of course, you can trust Homebrew
😉 But do you trust the multi-megabyte Makefile that Homebrew runs? Developers
often understand C++ far better than they understand make syntax. It’s too high
a risk to sudo such stuff. It could modify (or upload) any files on your
system. And indeed, we’ve seen some build scripts try to modify `/usr` even when
the prefix was specified as something else entirely.

We use the macOS sandbox to stop this but this doesn't work when run as the `root` user (which also has read and write access to almost everything on the system).

Did you `chown root /Applications/TextMate.app`? Probably
not. So is it that important to `chown root wget`?

If you need to run Homebrew in a multi-user environment, consider
creating a separate user account especially for use of Homebrew.

## Why isn’t a particular command documented?

If it’s not in `man brew`, it’s probably an external command. These are documented [here](External-Commands.md).

## Why haven’t you pulled my pull request?
If it’s been a while, bump it with a “bump” comment. Sometimes we miss requests and there are plenty of them. Maybe we were thinking on something. It will encourage consideration. In the meantime if you could rebase the pull request so that it can be cherry-picked more easily we will love you for a long time.

## Can I edit formulae myself?
Yes! It’s easy! Just `brew edit <formula>`. You don’t have to submit modifications back to `homebrew/core`, just edit the formula as you personally need it and `brew install`. As a bonus `brew update` will merge your changes with upstream so you can still keep the formula up-to-date **with** your personal modifications!

## Can I make new formulae?
Yes! It’s easy! Just `brew create URL`. Homebrew will then open the formula in
`EDITOR` so you can edit it, but it probably already installs; try it: `brew
install <formula>`. If you encounter any issues, run the command with the
`--debug` switch like so: `brew install --debug <formula>`, which drops you
into a debugging shell.

If you want your new formula to be part of `homebrew/core` or want
to learn more about writing formulae, then please read the [Formula Cookbook](Formula-Cookbook.md).

## Can I install my own stuff to `/usr/local`?
Yes, `brew` is designed to not get in your way so you can use it how you
like.

Install your own stuff, but be aware that if you install common
libraries like libexpat yourself, it may cause trouble when trying to
build certain Homebrew formula. As a result `brew doctor` will warn you
about this.

Thus it’s probably better to install your own stuff to the Cellar and
then `brew link` it. Like so:

```sh
$ cd foo-0.1
$ brew diy
./configure --prefix=/usr/local/Cellar/foo/0.1
$ ./configure --prefix=/usr/local/Cellar/foo/0.1
[snip]
$ make && make install
$ brew link foo
Linking /usr/local/Cellar/foo/0.1… 17 symlinks created
```

## Why was a formula deleted?
Use `brew log <formula>` to find out! Likely because it had unresolved issues or
our analytics identified it was not widely used.

## Homebrew is a poor name, it's too generic, why was it chosen?
@mxcl was too concerned with the beer theme and didn’t consider that the
project may actually prove popular. By the time he realised it was, it was too
late. However, today, the first Google hit for “homebrew” is not beer
related ;&#8209;)

## What does "keg-only" mean?
It means the formula is installed only into the Cellar; it is not linked
into `/usr/local`. This means most tools will not find it. We don’t do
this for stupid reasons. You can still link in the formula if you need
to with `brew link`.

## How can I specify different configure arguments for a formula?
`brew edit <formula>` and edit the formula. Currently there is no
other way to do this.

## Is there a glossary of terms around?
All your terminology needs can be [found here](Formula-Cookbook.md#homebrew-terminology).
