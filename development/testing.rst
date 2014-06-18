Testing
*******

All Release Engine code includes unit tests to verify expected
functionality. In the rest of this section we'll learn how the unit
tests are put together and how to interact with them.

Components
++++++++++

Release Engine unit tests are integrated with/depend on the following
items:

* `Travis CI <https://travis-ci.org/>`_ - Free online service
  providing `continuous integration` functionality for open source
  projects. Tests are ran automatically on every git commit.

* `unittest <https://docs.python.org/2/library/unittest.html>`_ -
  Python unit testing framework. All Release Engine tests are written
  using this framework.

* `nose <https://nose.readthedocs.org/en/latest/>`_ - Per the **nose**
  website: "`extends unittest to make testing easier`". **nose** is
  used to run our unit tests.

* `coverage <http://nedbatchelder.com/code/coverage/>`_ - A tool for
  measuring code coverage of Python programs. For the Release Engine
  we require a minimum test coverage of **80%**. This is invoked by
  **nose** automatically.

* `mock <http://www.voidspace.org.uk/python/mock/>`_ - A library for
  testing in Python. It allows you to replace parts of your `system
  under test` with mock objects (such as fake REST endpoints) and make
  assertions about how they have been used.

* `pep8 <https://pypi.python.org/pypi/pep8>`_ - A tool to check Python
  code against some of the style conventions in :pep:`0008`.

* `pyflakes <https://pypi.python.org/pypi/pyflakes>`_ - A simple
  program which checks Python source files for errors.

* `Makefiles <http://www.gnu.org/software/make/>`_ - Utility scripts
  used for project building and testing. How Release Engine uses
  **Makefiles** is described later in this section.

Requirements
++++++++++++

* ``python-nose``
* ``python-coverage``
* ``python-mock``

Some components may have additional test requirements. For example,
:ref:`re-worker-func <re_worker_func>` requires ``pyOpenSSL``, which
requires ``openssl-devel``, ``openssl-libs``, and
``libffi-devel``. Additionally, :ref:`re-rest <re_rest>` requires
``openldap-devel`` to run its unit tests.

.. todo:: Document other test dependencies


Targets
+++++++

In the scope of this document and testing, we use the term `target` in
the context of `makefile targets`. For the purpose of this
documentation, we can think of these `targets` as pre-defined commands
coded in a makefile. Release Engine testing targets include:

* ``ci`` - Run the tests exactly how they are ran in Travis-CI
* ``pep8`` - Run :pep:`0008` syntax checks
* ``pyflakes`` - Run `pyflakes` error checks
* ``clean`` - Remove temporary files and build artifacts from the
  checked-out repository.
* ``tests`` - A quicker version of ``ci``. Different from ``ci`` in
  that ``tests`` uses libraries installed on the local development
  workstation. ``tests`` runs the **unittests**, **pep8** tests, and
  **pyflakes** tests automatically.

To ensure the highest degree of confidence in test results you should
always use the ``ci`` target.

When Travis-CI runs an integration test, it calls the ``ci`` target.

Running the Tests
+++++++++++++++++

The Release Engine test suite is invoked via the Makefile. The
following is an example of how to run the ``ci`` test target manually
on the :ref:`re-core <re_core>` component.


.. code-block:: console
   :linenos:
   :emphasize-lines: 1,29,33

   [~/re-core]$ make ci

   #############################################
   # Creating a virtualenv
   #############################################
   virtualenv re-coreenv
   New python executable in re-coreenv/bin/python
   Installing Setuptools..............................................................................................................................................................................................................................done.
   Installing Pip.....................................................................................................................................................................................................................................................................................................................................done.
   . re-coreenv/bin/activate && pip install -r requirements.txt
   Downloading/unpacking pika>=0.9.12 (from -r requirements.txt (line 1))

   ... snip ...

   Successfully installed pep8 nose coverage mock
   Cleaning up...
   #############################################
   # Listing all pip deps
   #############################################
   . re-coreenv/bin/activate && pip freeze
   coverage==3.7.1
   mock==1.0.1
   nose==1.3.3
   pep8==1.5.7
   pika==0.9.13
   pymongo==2.7.1
   wsgiref==0.1.2
   #############################################
   # Running PEP8 Compliance Tests in virtualenv
   #############################################
   . re-coreenv/bin/activate && pep8 --ignore=E501,E121,E124 src/recore/
   #############################################
   # Running Unit Tests in virtualenv
   #############################################
   . re-coreenv/bin/activate && nosetests -v --with-cover --cover-min-percentage=80 --cover-package=recore test/
   Verify using init_amqp provides us with a connection ... ok
   Loggers are created with appropriate handlers associated ... ok

   ... snip ...

   Verify create_json_str produces proper json ... ok
   Verify load_json_str produces proper structures ... ok
   Verify config parsing works as expected. ... ok

   Name                Stmts   Miss  Cover   Missing
   -------------------------------------------------
   recore                 36      0   100%
   recore.amqp            72      4    94%   79, 169-172
   recore.constants        1      0   100%
   recore.fsm            179     25    86%   97-103, 148-152, 199-249
   recore.job              0      0   100%
   recore.job.create      25      0   100%
   recore.mongo           62      5    92%   92-100
   recore.utils           13      0   100%
   -------------------------------------------------
   TOTAL                 388     34    91%
   ----------------------------------------------------------------------
   Ran 35 tests in 0.047s

   OK
   :

On line **1** we see how to call a makefile target. In this case it's
quite straightforward: ``make ci``. Other targets are called in the
same way. For example, to run the ``clean`` target, you run the
command ``make clean``.

On line **29** we see a header printed, `Running PEP8 Compliance Tests
in virtualenv`. By calling the ``ci`` target, **make** automatically
knows what other targets must be called as well, such as ``ci-pep8``
and ``ci-unittests`` (seen on line **33**).


Troubleshooting
+++++++++++++++

If you find yourself unable to run the unit tests:

#. `Search <https://www.google.com>`_ for relevant error messages

#. **Read** the error message closely. The solution could be hidden in
   the error message output. The problem could be as simple as a
   missing dependency

#. If you are unable to figure out all the necessary dependencies to
   run the tests, file an issue on that specific projects GitHub issue
   tracker. Include the full error message.
