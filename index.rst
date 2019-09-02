.. minisam-website documentation master file, created by
   sphinx-quickstart on Thu Jul  4 00:32:48 2019.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

miniSAM
===========================================

`Link to Github repository <https://github.com/dongjing3309/minisam/>`_

............................................................................

miniSAM is an open-source C++/Python framework for solving factor graph based least squares problems. The APIs and implementation of miniSAM are heavily inspired and influenced by `GTSAM <https://gtsam.org/>`_, a famous factor graph framework, but miniSAM is a much more lightweight framework with

- Full Python/NumPy API, which enables more agile development and easy binding with existing Python projects, and
- A wide list of sparse linear solvers, including CUDA enabled sparse linear solvers.

miniSAM is developed by `Jing Dong <mailto:thu.dongjing@gmail.com>`_ and `Zhaoyang Lv <mailto:zhaoyang.lv@gatech.edu>`_. This work was initially started as final project of `Math 6644 <https://www.cc.gatech.edu/~echow/cse6644-17.html>`_ back to 2017, and mostly finished part-time when both authors were PhD students at College of Computing, Georgia Institute of Technology.

Get started
------------------------------

.. toctree::
   :maxdepth: 1
   
   C++ compile and installation <install>
   Python installation <install_python>
   Use Github issue for questions <github_issue>
   FAQ <faq>

Factor graph theory
------------------------------

.. toctree::
   :maxdepth: 1
   
   Introduction to factor graph <factor_graph>
      
Examples
------------------------------

.. toctree::
   :maxdepth: 1
   
   2D pose graph example <pose_graph_2d>
   GPS-like factor example <gps_example>
   Robust curve fitting <robust_curve_fitting>
   Optimizing your own types <optimize_type>


Questions & Bug Reporting
---------------------------------

Please use Github issue tracker for general questions and reporting bugs.

Citing
---------------------------------

If you use miniSAM in an academic context, please cite following publications:

.. code-block:: text

   @article{Dong19ppniv,
     author    = {Jing Dong and Zhaoyang Lv},
     title     = {mini{SAM}: A Flexible Factor Graph Non-linear Least Squares Optimization Framework},
     year      = {2019},
   }

License
---------------------------------

miniSAM is released under the BSD license, reproduced in the file LICENSE in this directory. 
Note that the linked sparse linear solvers have different licenses, see `this page <file:///home/jing/code/minisam-website/build/install.html#sparse-solvers-license>`_ for details