
C++ Compile and Installation
===========================================

In this page we will go through how to compile and install miniSAM.

Dependencies
-----------------------------------

miniSAM is written in C++11 and designed to have minimal external dependencies, 
making it lightweight and has great cross-platform compatibility.
Many external dependencies are optional, and miniSAM will detect whether these dependencies are installed or not,
and compile its modules accordingly.

Mandatory Prerequisites
~~~~~~~~~~~~~~~~~~~~~~~~~~

- `CMake <https://cmake.org/>`_ 3.4+ (Ubuntu: :code:`sudo apt-get install cmake`), compilation configuration tool.
- `Eigen <http://eigen.tuxfamily.org>`_ 3.3.0+ (Ubuntu: :code:`sudo apt-get install libeigen3-dev`), a C++ template library for linear algebra. miniSAM use Eigen for all dense linear algebra and some sparse linear algebra.

Optional Dependencies
~~~~~~~~~~~~~~~~~~~~~~~~~~

- `Sophus <https://github.com/strasdat/Sophus>`_, a C++ implementation of Lie Groups using Eigen. miniSAM uses Sophus for all SLAM/multi-view geometry functionalities, also miniSAM needs Sophus to compile/run most examples, so **Sophus installation is highly recommended**.
- `Python <http://www.python.org/>`_ 2.7/3.4+ to use miniSAM Python package.
- `SuiteSparse <http://faculty.cse.tamu.edu/davis/suitesparse.html>`_ (Ubuntu: :code:`sudo apt-get install libsuitesparse-dev`), a suite of sparse matrix algorithms. miniSAM has option to use CHOLMOD and SPQR sparse linear solvers. **CHOLMOD solver is highly recommended when solving large scale problems on CPU**.
- `CUDA <https://developer.nvidia.com/cuda-downloads>`_ 7.5+. miniSAM has option to use cuSOLVER Cholesky sparse linear solver.

Compile and install
-----------------------------------

The miniSAM library is designed to be cross-platform, should be compatible with any modern compiler which supports C++11.
On Ubuntu Linux to simply get and compile (and install) the library:

.. code-block:: bash

   git clone --recurse-submodules https://github.com/dongjing3309/minisam.git
   mkdir build
   cd build
   cmake ..
   make
   make check  # optional, run unit tests
   make install  # optional, install the lib

After running CMake you will see a compile summary like:

.. code-block:: text

   -- ===============================================================
   --                 Configurations for miniSAM                     
   --  
   --   Library type                Shared
   --   Install path                /usr/local
   --  
   --   Compiler type               GNU
   --   Compiler version            5.4.0
   --   Built type                  Release
   --   C++ compilation flags        -std=c++11 -Wall -Werror -Wextra -O3 -DNDEBUG
   --  
   --   Multi-threading             No
   --   Internal profiling          No
   --  
   --   Eigen                       3.3.5 (/usr/local/include/eigen3)
   --   Sophus                      Yes (/usr/local/share/sophus/cmake)
   --   Cholmod                     Yes (/usr/include/suitesparse)
   --   SPQR                        Yes (/usr/include/suitesparse)
   --   CUDA cuSOLVER               No
   --  
   --   Python package              Yes
   --   Python version              3.5
   --   Python executable           /usr/bin/python3.5


CMake options
~~~~~~~~~~~~~~~~~~~~~~~~~~

- :code:`CMAKE_BUILD_TYPE` (default Release) : CMake compile mode, four modes are available: Debug, Release, RelWithDebInfo and MinSizeRel.
- :code:`CMAKE_INSTALL_PREFIX` : Where the C++ library is installed. On Linux the default install path is :code:`/usr/local`, and on Windows is :code:`C:\Program Files\minisam` by default.

- :code:`MINISAM_BUILD_PYTHON_PACKAGE` (default OFF) : Whether the Python package is compiled. If this is set to ON, CMake will detect installed Python version and compile the python package accordingly. If both Python 2 and 3 are installed, Python 3 will be chosed.
- :code:`PYTHON_EXECUTABLE` : The Python version is used to compile the Python package.
- :code:`MINISAM_BUILD_SHARED_LIB` (default ON) : If set to ON, shared library is compiled. If set to OFF, static library is compiled. 

.. note:: 
   When :code:`MINISAM_BUILD_PYTHON_PACKAGE` is set to ON, :code:`MINISAM_BUILD_SHARED_LIB` must be set to ON.

- :code:`MINISAM_WITH_MULTI_THREADS` (default OFF) : 
- :code:`MINISAM_WITH_MULTI_THREADS_NUM` (default 4) : When :code:`MINISAM_WITH_MULTI_THREADS` is set to ON, the number of threads are used to internally.

.. note:: 
   The runtime speed is not likely be faster when :code:`MINISAM_WITH_MULTI_THREADS_NUM` is set to more than 4.

- :code:`MINISAM_WITH_INTERNAL_TIMING` (default OFF) : If the flag is set to OFF, only items set by users by :code:`tic()/toc()` will be shown when call :code:`global_timer().print()`

.. code-block:: text

   Profiling item                       Total    Freq       Avg       Max       Min
   --------------------------------------------------------------------------------
   Bundle adjustment all              4108 ms       1   4108 ms   4108 ms   4108 ms


When the flag is set to ON, the timer prints an addtional list of major internal timing results, with item names starts with :code:`*`. Example prints:

.. code-block:: text

   Profiling item                       Total    Freq       Avg       Max       Min
   --------------------------------------------------------------------------------
   * Graph error                       129 ms      23   5608 us   5854 us   4829 us
   * Graph linearization              1578 ms      21  75.16 ms  79.14 ms  71.62 ms
   * Linear system solve              2086 ms      22  94.82 ms  111.2 ms  88.81 ms
   * Linearization pattern           21.29 ms       1  21.29 ms  21.29 ms  21.29 ms
   * Ordering/LinearSolver init      105.4 ms       1  105.4 ms  105.4 ms  105.4 ms
   * Solution update                 49.25 ms      22   2238 us   2351 us   2087 us
   Bundle adjustment all              4012 ms       1   4012 ms   4012 ms   4012 ms


- :code:`MINISAM_WITH_SOPHUS` (default ON) : When set to ON, CMake will search for installed Sophus, and compile corresponding miniSAM modules only when installed Sophus is found. If set to OFF, CMake will not search installed Sophus.
- :code:`MINISAM_WITH_CHOLMOD/MINISAM_WITH_SPQR/MINISAM_WITH_CUSOLVER` (default ON) : When set to ON, CMake will search for installed solvers, and compile corresponding miniSAM modules only when installed solvers is found. If set to OFF, CMake will not search installed corresponding solvers.

Sparse solvers license
-----------------------------------

miniSAM currently provides a wide set of wrappers to external sparse linear solver libraries. Although miniSAM is a BSD-licensed library, linked external solver libraries have different licenses. They are summarized in the following table:

================================  ===================================  ==========
Solver                            :code:`minisam::LinearSolverType`    License   
================================  ===================================  ==========
Eigen Cholesky LDLT               :code:`CHOLESKY`                     LGPL
SuiteSparse CHOLMOD               :code:`CHOLMOD`                      GPL3  
SuiteSparse SPQR                  :code:`QR`                           GPL3  
Eigen Conjugate Gradient          :code:`CG`                           MPL2
Eigen LSCG                        :code:`LSCG`                         MPL2 
CUDA cuSolver Cholesky            :code:`CUDA_CHOLESKY`                EULA, AMD ordering uses License LGPL
Eigen Schur dense Cholesky        :code:`SCHUR_DENSE_CHOLESKY`         MPL2   
================================  ===================================  ==========
