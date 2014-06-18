Contributing
************

This section describes the guidelines for contributing to the Release
Engine.

General Guidelines
++++++++++++++++++

Please conform to :pep:`0008` for code formatting. This specification
outlines the highlights that we require above and beyond. Your code
must follow this (or note why it can't) before patches will be
accepted. There is one consistent exception to this rule:

**E501**
   Line too long

The ``pep8`` tests for the Release Engine include a ``--ignore``
option to specifically exclude **E501** from the tests.


Write `unittests
<https://github.com/ashcrow/flagon/tree/master/test>`_ for any new
functionality, `if you are up to the task`. Not a requirement, but it
does get you a lot of karma.


Write `intelligent commit messages
<http://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html>`_.


Issue Reporting
+++++++++++++++

If you are reporting an issue with the Release Engine, please use the
following template when describing your issue::


   Description of the issue (include full error messages):

   How to reproduce the issue:

   How reproducable (every time? intermittently?):

   Version of the product effected (git hashes are OK):

   Your operating system release-version:

   What you expected to happen:

   What actually happened:
