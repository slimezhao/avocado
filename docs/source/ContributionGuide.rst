================================
Contribution and Community Guide
================================

Useful pointers on how to participate of the Avocado community and contribute.

.. _hacking-and-using:

Hacking and Using Avocado
=========================

Since version 0.31.0, our plugin system requires Setuptools entry points to be
registered. If you're hacking on Avocado and want to use the same, possibly modified,
source for running your tests and experiments, you may do so with one additional step::

  $ make develop

On POSIX systems this will create an "egg link" to your original source tree under
"$HOME/.local/lib/pythonX.Y/site-packages". Then, on your original source tree, an
"egg info" directory will be created, containing, among other things, the Setuptools
entry points mentioned before. This works like a symlink, so you only need to run
this once (unless you add a new entry-point, then you need to re-run it to make it
available).

Avocado supports various plugins, which are distributed as separate projects,
for example "avocado-vt" and "avocado-virt". These also need to be
deployed and linked in order to work properly with the avocado from
sources (installed version works out of the box). To simplify this you can
use `make requirements-plugins` from the main avocado project to install
requirements of the plugins and `make link` to link and develop the
plugins. The workflow could be::

    $ cd $AVOCADO_PROJECTS_DIR
    $ git clone $AVOCADO_GIT
    $ git clone $AVOCADO_PROJECT2
    $ # Add more projects
    $ cd avocado    # go into the main avocado project dir
    $ make requirements-plugins
    $ make link

You should see the process and status for each directory.

Contact information
===================

- Avocado-devel mailing list: `https://www.redhat.com/mailman/listinfo/avocado-devel <https://www.redhat.com/mailman/listinfo/avocado-devel>`_
- Avocado IRC channel: `irc.oftc.net #avocado <irc://irc.oftc.net/#avocado>`_

Contributing to Avocado
=======================

Avocado uses github and the github pull request development model. You can
find a primer on how to use github pull requests
`here <https://help.github.com/articles/using-pull-requests>`_. Every Pull
Request you send will be automatically tested by
`Travis CI <https://travis-ci.org/avocado-framework/avocado>`_ and review will
take place in the Pull Request as well.

For people who don't like the github development model, there is the option
of sending the patches to the Mailing List, following a workflow more
traditional in Open Source development communities. The patches will be
reviewed in the Mailing List, should you opt for that. Then a maintainer will
collect the patches, integrate them on a branch, and then those patches will
be submitted as a github Pull Request. This process tries to ensure that every
contributed patch goes through the CI jobs before it is considered good for
inclusion.

Git workflow
------------

- Fork the repository in github.

- Clone from your fork::

    $ git clone git@github.com:<username>/avocado.git

- Enter the directory::

    $ cd avocado

- Create a ``remote``, pointing to the upstream::

    $ git remote add upstream git@github.com:avocado-framework/avocado.git

- Configure your name and e-mail in git::

    $ git config --global user.name "Your Name"
    $ git config --global user.email email@foo.bar

- Golden tip: never work on local branch master. Instead, create a new
  local branch and checkout to it::

    $ git checkout -b my_new_local_branch

- Code and then commit your changes::

    $ git add new-file.py
    $ git commit -s
    # or "git commit -as" to commit all changes

- Write a good commit message, pointing motivation, issues that you're
  addressing. Usually you should try to explain 3 points in the commit
  message: motivation, approach and effects::

    header          <- Limited to 72 characters. No period.
                    <- Blank line
    message         <- Any number of lines, limited to 72 characters per line.
                    <- Blank line
    Reference:      <- External references, one per line (issue, trello, ...)
    Signed-off-by:  <- Signature and acknowledgment of licensing terms when
                       contributing to the project (created by git commit -s)

- Make sure your code is working (install your version of avocado, test
  your change, run ``make check`` to make sure you didn't introduce any
  regressions).

- Paste the ``job.log`` file content from the previous step in a pastebin
  service, like fpaste.org. If you have ``fpaste`` installed, you can
  simply run::

    $ fpaste ~/avocado/job-results/latest/job.log

- Rebase your local branch on top of upstream master::

    $ git fetch
    $ git rebase upstream/master
    (resolve merge conflicts, if any)

- Push your commit(s) to your fork::

    $ git push origin my_new_local_branch

- Create the Pull Request on github. Add the relevant information to the
  Pull Request description.

- In the Pull Request discussion page, comment with the link to the
  job.log output/file.

- Check if your Pull Request passes the CI (travis). Your Pull Request
  will probably be ignored until it's all green.

Now you're waiting for feedback on github Pull Request page. Once you
get some, join the discussion, answer the questions, make clear if you're
going to change the code based on some review and, if not, why. Feel free
to disagree with the reviewer, they probably have different use cases and
opinions, which is expected. Try describing yours and suggest other
solutions, if necessary.

New versions of your code should not be force-updated (unless explicitly
requested by the code reviewer). Instead, you should:

- Create a new branch out of your previous branch::

    $ git checkout my_new_local_branch
    $ git checkout -b my_new_local_branch_v2

- Code, and amend the commit(s) and/or create new commits. If you have
  more than one commit in the PR, you will probably need to rebase
  interactively to amend the right commits. ``git cola`` or ``git citool``
  can be handy here.

- Rebase your local branch on top of upstream master::

    $ git fetch
    $ git rebase upstream/master
    (resolve merge conflicts, if any)

- Push your changes::

    $ git push origin my_new_local_branch_v2

- Create a new Pull Request for this new branch. In the Pull Request
  description, point the previous Pull Request and the changes the current
  Pull Request introduced when compared to the previous Pull Request(s).

- Close the previous Pull Request on github.

After your PR gets merged, you can sync the master branch on your local
repository propagate the sync to the master branch in your fork
repository on github::

    $ git checkout master
    $ git pull upstream master
    $ git push

From time to time, you can remove old branches to avoid pollution::

    # To list branches along with time reference:
    $ git for-each-ref --sort='-authordate:iso8601' --format=' %(authordate:iso8601)%09%(refname)' refs/heads
    # To remove branches from your fork repository:
    $ git push origin :my_old_branch

Signing commits
---------------

Optionally you can sign the commits using GPG signatures. Doing
it is simple and it helps from unauthorized code being merged without notice.

All you need is a valid GPG signature, git configuration, slightly modified
workflow to use the signature and eventually even setup in github so one
benefits from the "nice" UI.

Get a GPG signature::

    # Google for howto, but generally it works like this
    $ gpg --gen-key  # defaults are usually fine (using expiration is recommended)
    $ gpg --send-keys $YOUR_KEY    # to propagate the key to outer world

Enable it in git::

    $ git config --global user.signingkey $YOUR_KEY

(optional) Link the key with your GH account::

    1. Login to github
    2. Go to settings->SSH and GPG keys
    3. Add New GPG key
    4. run $(gpg -a --export $YOUR_EMAIL) in shell to see your key
    5. paste the key there

Use it::

    # You can sign commits by using '-S'
    $ git commit -S
    # You can sign merges by using '-S'
    $ git merge -S

.. warning::
   You can not use the merge button on github to do signed merges as github
   does not have your private key.

Licensing
---------

Except where otherwise indicated in a given source file, all original
contributions to Avocado are licensed under the GNU General Public
License version 2 `(GPLv2) <https://www.gnu.org/licenses/gpl-2.0.html>`_
or any later version.

By contributing you agree that these contributions are your own (or
approved by your employer) and you grant a full, complete, irrevocable
copyright license to all users and developers of the Avocado project,
present and future, pursuant to the license of the project.

Code Review
-----------

Every single Pull Request in Avocado has to be reviewed by at least one
other developer. All members of the core team have permission to merge
a Pull Request, but there are some conditions that have to be fulfilled
before merging the code:

- Pull Request has to pass the CI tests.
- One 'Approved' code review should be given.
- No explicit disapproval should be present.

Pull Requests failing in CI will not be merged, and reviews won't be
given to them until all the problems are sorted out. In case of a weird
failure, or false-negative (eg. due to too many commits in a single
PR), please reach the developers by @name/email/irc or other means.

While reviewing the code, one should:

- Verify that the code is sound and clean.
- Run the highest level of selftests per each new commit in the merge.
  The ``contrib/scripts/avocado-check-pr.sh`` contrib script should
  simplify this step.
- Verify that code works to its purpose.
- Make sure the commits organization is proper (i.e. code is well
  organized in atomic commits, there's no extra/unwanted commits, ...).
- Provide an in-line feedback with explicit questions and/or requests of
  improvements.
- Provide a general feedback in the review message, being explicit about
  what's expected for the next Pull Request version, if that's the case.

When the Pull Request is approved, the reviewer will merge the code or
wait for someone with merge permission to merge it.

Using ``avocado-check-pr.sh``
-----------------------------

The ``contrib/scripts/avocado-check-pr.sh`` script is here to simplify
the per-commit-check. You can simply prepare the merge and initiate
``AVOCADO_CHECK_LEVEL=99 contrib/scripts/avocado-check-pr.sh`` to run
all checks per each commit between your branch and the same branch
on the ``origin/master`` (you can specify different remote origin).

Use ``./contrib/scripts/avocado-check-pr.sh -h`` to learn more about
the options. I can recommend:
``AVOCADO_PARALLEL_CHECK=yes AVOCADO_CHECK_LEVEL=99 ./contrib/scripts/avocado-check-pr.sh -i -v`` and due to PARALLEL false-negatives running
``while :; do read AAA; python -m unittest $AAA; done`` in second
terminal to re-check potential failures.

.. note:: Before first use you might need to create
          ``~/.config/github_checker.ini`` and fill github user/token
          entries (while on it you can also specify some defaults)


Tests Repositories
==================

We encourage you or your company to create public Avocado tests
repositories so the community can also benefit of your tests. We will be
pleased to advertise your repository here in our documentation.

List of known community and third party maintained repositories:

- https://github.com/avocado-framework-tests/avocado-misc-tests:
  Community maintained Avocado miscellaneous tests repository. There you
  will find, among others, performance tests like ``lmbench``,
  ``stress``, cpu tests like ``ebizzy`` and generic tests like ``ltp``.
  Some of them were ported from Autotest Client Tests repository.

- https://github.com/scylladb/scylla-cluster-tests:
  Avocado tests for Scylla Clusters. Those tests can automatically create
  a scylla cluster, some loader machines and then run operations defined by
  the test writers, such as database workloads.
