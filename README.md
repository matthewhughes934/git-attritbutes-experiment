# README

An experiment using [`git` attributes](https://git-scm.com/docs/gitattributes)
to define a custom:

  - diff driver
  - merge driver
  - filter

For working with base 64 encoded data. Using these, files with the corresponding
attributes will be stored as base 64 encoded data in `git` but appear in plain
text in the worktree, log, diffs etc., see the `driver/` directory for details.

Using these, we can apply patches directly on the base 64 encoded content, which
would usually conflict, as long as the decoded content does not conflict, see
the `patches/` director for examples.

## Demonstration

Without any setup, trying to apply those patches on top of each other will fail,
you can observe this by running

    # check contents of file
    $ cat ./file.b64
    # try and merge patches on the base 64 file (exepected to fail)
    $ ./run_test

To use the drivers we need to tell `git` to use them, see `./setup_attributes`
for some details.

    # stop the rebase our previous command attempted
    $ git rebase --abort
    $ git switch main
    $ ./setup_attributes
    # reset the file in the working tree so our filters are applied
    $ rm ./file.b64 && git checkout ./file.b64
    # file in work tree should now be decoded
    $ cat ./file.b64
    # observe the checked-in content of the file (should be base 64)
    $ git show :file.b64
    # re-run the tests
    $ ./run_test
    # observe the patches applied
    $ cat file.b64
