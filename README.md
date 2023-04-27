# Cockpit Session Recording

Module for [Cockpit](http://www.cockpit-project.org) which provides session recording 
configuration and playback. 
It requires [tlog](https://github.com/Scribery/tlog) to record terminal sessions. 
SSSD is required to manage which users / groups are recorded. Systemd Journal is used to store recordings.
Ansible role for session-recording is [here](https://github.com/nkinder/session-recording).

Demos & Talks:

 * [Demo 1 on YouTube](https://youtu.be/5-0WBf4rOrc)
 * [Demo 2 on YouTube](https://youtu.be/Fw8g_fFvwcs)
 * [FOSDEM talk](https://youtu.be/sHO5y28EHXg)
 
GitHub Organization: 
 
 * [scribery.github.io](http://scribery.github.io/)
 * [Scribery](https://github.com/Scribery)

This project is based on the [Cockpit Starter Kit](https://github.com/cockpit-project/starter-kit).
See [Starter Kit Intro](http://cockpit-project.org/blog/cockpit-starter-kit.html) for details.

# Getting and building the source

Make sure you have `npm` available (usually from your distribution package).
These commands check out the source and build it into the `dist/` directory:

```
git clone https://github.com/Scribery/cockpit-session-recording.git
cd cockpit-session-recording
make
```

# Installing

`make install` compiles and installs the package in `/usr/local/share/cockpit/`. The
convenience targets `srpm` and `rpm` build the source and binary rpms,
respectively. Both of these make use of the `dist` target, which is used
to generate the distribution tarball. In `production` mode, source files are
automatically minified and compressed. Set `NODE_ENV=production` if you want to
duplicate this behavior.

For development, you usually want to run your module straight out of the git
tree. To do that, run `make devel-install`, which links your checkout to the
location were cockpit-bridge looks for packages. If you prefer to do
this manually:

```
mkdir -p ~/.local/share/cockpit
ln -s `pwd`/dist ~/.local/share/cockpit/session-recording
```

After changing the code and running `make` again, reload the Cockpit page in
your browser.

You can also use
[watch mode](https://esbuild.github.io/api/#watch) to
automatically update the bundle on every code change with

    $ npm run watch

or

    $ make watch

When developing against a virtual machine, watch mode can also automatically upload
the code changes by setting the `RSYNC` environment variable to
the remote hostname.

    $ RSYNC=c make watch

When developing against a remote host as a normal user, `RSYNC_DEVEL` can be
set to upload code changes to `~/.local/share/cockpit/` instead of
`/usr/local`.

    $ RSYNC_DEVEL=example.com make watch

To "uninstall" the locally installed version, run `make devel-uninstall`, or
remove manually the symlink:

    rm ~/.local/share/cockpit/starter-kit

# Running eslint

Cockpit Starter Kit uses [ESLint](https://eslint.org/) to automatically check
JavaScript code style in `.js` and `.jsx` files.

eslint is executed within every build.

For developer convenience, the ESLint can be started explicitly by:

    $ npm run eslint

Violations of some rules can be fixed automatically by:

    $ npm run eslint:fix

Rules configuration can be found in the `.eslintrc.json` file.

## Running stylelint

Cockpit uses [Stylelint](https://stylelint.io/) to automatically check CSS code
style in `.css` and `scss` files.

styleint is executed within every build.

For developer convenience, the Stylelint can be started explicitly by:

    $ npm run stylelint

Violations of some rules can be fixed automatically by:

    $ npm run stylelint:fix

Rules configuration can be found in the `.stylelintrc.json` file.

During fast iterative development, you can also choose to not run eslint/stylelint.
This speeds up the build and avoids build failures due to e. g. ill-formatted
css or other issues:

    $ make LINT=0

# Running tests locally

Run `make check` to build an RPM, install it into a standard Cockpit test VM
(centos-8-stream by default), and run the test/check-application integration test on
it. This uses Cockpit's Chrome DevTools Protocol based browser tests, through a
Python API abstraction. Note that this API is not guaranteed to be stable, so
if you run into failures and don't want to adjust tests, consider checking out
Cockpit's test/common from a tag instead of main (see the `test/common`
target in `Makefile`).

After the test VM is prepared, you can manually run the test without rebuilding
the VM, possibly with extra options for tracing and halting on test failures
(for interactive debugging):

    TEST_OS=centos-8-stream test/check-application -tvs

It is possible to setup the test environment without running the tests:

    TEST_OS=centos-8-stream make prepare-check

You can also run the test against a different Cockpit image, for example:

    TEST_OS=fedora-34 make check
