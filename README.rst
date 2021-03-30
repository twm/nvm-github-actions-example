.. This document © 2021 Tom Most. All rights reserved.
.. Code examples provided under the MIT license. See the file LICENSE.

===========================
Using nvm on GitHub Actions
===========================

GitHub Actions `virtual environments`_ (VM images) `include nvm`_, the `Node Version Manager <nvm>`_,
the ``nvm`` command doesn't work by default on GitHub Actions (GHA).
This document describes how to get it working,
and demonstrates how to integrate nvm and npm with GHA's handy caching functionality to speed up your builds.

Getting nvm Working
===================

Why doesn't the ``nvm`` command work by default?
Recall that when you `install nvm`_ it adds a snippet to your shell's startup scripts.
For Bash, it's added to ``~/.bash_profile``, which is only sourced (included) as part of *interactive* shell startup [1]_.
A GHA `run step`_ runs Bash with flags that skip sourcing that file, by default::

    bash --noprofile --norc -eo pipefail {0}

``nvm`` will work if you manually source ``nvm.sh`` in your step:

.. code-block:: yaml

   jobs:
    steps:
    - run: |
        source "$NVM_DIR"/nvm.sh
        nvm install

That ``NVM_DIR`` environment variable is `part of the GHA environment <https://github.com/actions/virtual-environments/blob/826fed960459993d41c2f9310d220b7cf2c015e8/images/linux/scripts/installers/nvm.sh#L11>`_.

If you override the ``shell`` command to include the ``--login`` flag Bash will automatically do the sourcing:

.. code-block:: yaml

   jobs:
    steps:
    - run: |
        nvm install
      shell: "bash -eo pipefail --login {0}"

Both of these are a bit of a mouthful to repeat on each step, though.

.. TODO Explain the anchor trick

Enabling Caching
================

.. TODO


.. _virtual environments: https://github.com/actions/virtual-environments

.. _include nvm: https://github.com/actions/virtual-environments/blob/main/images/linux/scripts/installers/nvm.sh

.. permalink for the above https://github.com/actions/virtual-environments/blob/826fed960459993d41c2f9310d220b7cf2c015e8/images/linux/scripts/installers/nvm.sh

.. _nvm: https://github.com/nvm-sh/nvm

.. _install nvm: https://github.com/nvm-sh/nvm#install--update-script

.. [1] See the `INVOCATION section`_ of ``bash(1)``

.. _invocation section: https://manpages.ubuntu.com/manpages/focal/en/man1/bash.1.html#invocation

.. _run step: https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions#jobsjob_idstepsrun
