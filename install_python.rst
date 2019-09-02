
Python Installation
===========================================

Compile and install Python wrapper
-----------------------------------

Before you compile and install Python package, make sure you have done steps in `C++ compile and installation <install.html>`__.

Prerequisites
~~~~~~~~~~~~~~~~~~~~~~~~~~

- `Python <http://www.python.org/>`_: version 2.7/3.4+ is compatible miniSAM Python package.
- `NumPy <https://numpy.org/>`_ (:code:`pip install numpy`) : Numpy is required to use miniSAM Python package.
- `matplotlib <https://matplotlib.org/>`_ (:code:`pip install matplotlib`) : matplotlib is only needed to run miniSAM Python examples with visualization.

Compile and install
~~~~~~~~~~~~~~~~~~~~~~~~~~

On Ubuntu Linux to simply compile and install the Python package:

.. code-block:: bash

   cmake .. -DMINISAM_BUILD_PYTHON_PACKAGE=ON -DMINISAM_BUILD_SHARED_LIB=ON
   make python_package
   make install

After running CMake check the compile summary of the Python package:

.. code-block:: text

   --   Python package              Yes
   --   Python version              3.5
   --   Python executable           /usr/bin/python3.5

Install to Python virtualenv
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If the Python package is prefer to install a virtualenv location, set the :code:`PYTHON_EXECUTABLE` to the path of the Python binary, then compile and install the package.

If the virtualenv sandbox is setup at :code:`~/test_minisam` by

.. code-block:: bash

   ~$ virtualenv test_minisam

The python package can be installed to location :code:`~/test_minisam` by

.. code-block:: bash

   cmake .. -DPYTHON_EXECUTABLE=~/test_minisam/bin/python -DMINISAM_BUILD_PYTHON_PACKAGE=ON -DMINISAM_BUILD_SHARED_LIB=ON
   make python_package
   make install

Verify CMake information to check the package is installed by right Python executable

.. code-block:: text

   --   Python package              Yes
   --   Python version              2.7
   --   Python executable           /home/jing/test_minisam/bin/python