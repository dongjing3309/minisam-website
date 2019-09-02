
Introduction to Factor Graph
=============================================================

Here give an introduction to non-linear least squares and the connection between sparse least squares and factor graphs. 

Non-linear Least Square Optimization
------------------------------------------------------------


Non-linear least squares optimization is defined by 

.. math::
   x^{*} = \operatorname*{argmin}_{x} \sum_i \rho_i \big( \parallel f_i(x) \parallel^{2}_{{\Sigma}_i} \big), 
   :label: ls

where :math:`x \in \mathcal{M}` is a point on a general :math:`n`-dimensional manifold, :math:`x^{*} \in \mathcal{M}` is the solution, :math:`f_i \in \mathbb{R}^m` is a :math:`m`-dimensional vector-valued error function, :math:`\rho_i` is a robust kernel function, and :math:`{\Sigma}_i \in \mathbb{R}^{m \times m}` is a covariance matrix. The Mahalanobis distance is defined by :math:`\parallel v \parallel^{2}_{{\Sigma}} \doteq v^T {\Sigma}^{-1} v` where :math:`v \in \mathbb{R}^m` and :math:`{\Sigma}^{-1}` is the information matrix. If we factorize the information matrix by Cholesky factorization :math:`{\Sigma}^{-1} = {R}^T {R}`, where :math:`{R}` is upper triangular, we have

.. math::
   \parallel v \parallel^{2}_{{\Sigma}} = v^T {\Sigma}^{-1} v = v^T {R}^T {R} v
   = \parallel {R} v  \parallel^{2}. 
   :label: mahalanobis

If we consider the simplified case where :math:`\rho_i` is identity and define :math:`h_i(x) \doteq {R}_i f_i(x)`, then Eq. :eq:`ls` is equivalent to

.. math::
   x^{*} = \operatorname*{argmin}_{x} \sum_i \parallel h_i(x) \parallel^{2} 
   :label: ls_gaussian

as per Eq. :eq:`mahalanobis`. If we define a *linearization point* :math:`x_0 \in \mathcal{M}`, and the Jacobian matrix of :math:`h_i(x)`

.. math::
   {J}_i \doteq \frac{\partial h_i(x)}{\partial x} \Big|_{x=x_0} 
   :label: jacobian


then the Taylor expansion is given by

.. math::
   h_i(x_0 + \Delta x) = h_i(x_0) + {J}_i \Delta x + O(\Delta x^2),
   :label: linearization_taylor


which we can use to solve the least square problem by searching a *local* region near :math:`x_0`, and find the solution by iteratively solving a *linearized* least squares problem

.. math::
   \Delta x^{*} = \operatorname*{argmin}_{\Delta x} \sum_i \parallel {J}_i \Delta x + h_i(x_0)  \parallel^{2},
   :label: linear_ls


where :math:`\Delta x^{*} \in \mathbb{R}^n`, and the solution is updated by 

.. math::
   x^{*} = x_0 + \Delta x^{*}.
   :label: linear_ls_solution


If :math:`\mathcal{M}` is simply a vector space :math:`\mathbb{R}^n` then
the above procedure is performed iteratively in general by setting :math:`x_0` of next iteration from :math:`x^*` of current iteration, until :math:`x^*` converges. 
Trust-region policies like Levenberg-Marquardt can be also applied when looking for :math:`\Delta x^{*}`.


When :math:`\mathcal{M}` is a general manifold, we need to define a local coordinate chart of :math:`\mathcal{M}` near :math:`x_0`, which is an invertible map between a local region of :math:`\mathcal{M}` around :math:`x_0` and the local Euclidean space, and also an operator :math:`\oplus` that maps a point in local Euclidean space back to :math:`\mathcal{M}`. 
Thus Eq. :eq:`linear_ls_solution` on general manifolds is

.. math::
   x^{*} = x_0 \oplus \Delta x^{*}.
   :label: linear_ls_solution_manifold


A simple example of :math:`\oplus` is for the Euclidean space where it is simply the plus operator.

To solve the linear least squares problem in Eq. :eq:`linear_ls`, we first rewrite Eq. :eq:`linear_ls` as

.. math::
   \Delta x^{*} = \operatorname*{argmin}_{\Delta x} \parallel {J} \Delta x + b  \parallel^{2},
   :label: linear_ls_comb


where :math:`J` is defined by stacking all :math:`J_i` vertically, similarly :math:`b` is defined by stacking all :math:`h_i(x_0)` vertically.
Cholesky factorization is commonly used solve Eq. :eq:`linear_ls_comb`.  Since the solution of linear least squares problem in Eq. :eq:`linear_ls_comb` is given by the normal equation

.. math::
   J^T J \Delta x^{*} = J^T b,
   :label: normal_eq


we apply Cholesky factorization to symmetric :math:`J^T J`, and we have :math:`J^T J = R^T R` where :math:`R` is upper triangular. 
Then solving Eq. :eq:`normal_eq` is equivalent to solving both

.. math::
   &R^T y = J^T b\\
   &R \Delta x^{*} = y 
   :label: cholesky_2


in two steps, which can be both solved by back-substitution given that :math:`R` is triangular.
Other than Cholesky factorization, QR and SVD factorizations can be also used to solve Eq. :eq:`linear_ls_comb`, although with significantly slower speeds.
Iterative methods like pre-conditioned conjugate gradient (PCG) are also widely used to solve Eq. :eq:`normal_eq`, especially when :math:`J^T J` is very large.


Connection between Factor Graphs and Sparse Least Squares
------------------------------------------------------------

In previous literature [1]_ researchers have shown factor graphs have a tight connections with non-linear least square problems. 
A factor graph is a probabilistic graphical model, which represents a joint probability distribution of all factors

.. math::
   p(x) \propto \prod_i p_i(x_i),
   :label: factor_graph


where :math:`x_i \subseteq x` is a subset of variables involved in factor :math:`i`,
:math:`p(x)` is the overall distribution of the factor graph, and :math:`p_i(x_i)` is the distribution of each factor. 
The maximum a posteriori (MAP) estimate of the graph is

.. math::
   x^{*} = \operatorname*{argmax}_{x} p(x) = \operatorname*{argmax}_{x} \prod_i p_i(x_i).
   :label: factor_graph_map


If we consider the case where each factor has Gaussian distribution on :math:`f_i(x_i)` with covariance :math:`\Sigma_i`,

.. math::
   p_i(x_i) \propto \mathrm{exp} \big( - \frac{1}{2} \parallel f_i(x_i) \parallel^{2}_{{\Sigma}_i} \big),
   :label: gaussian_factor


then MAP inference is

.. math::
   x^{*} & = \operatorname*{argmax}_{x} \prod_i p_i(x_i) = \operatorname*{argmax}_{x} \mathrm{log} \big( \prod_i p_i(x_i) \big),  \\
   & = \operatorname*{argmin}_{x} \prod_i -\mathrm{log} \big( p_i(x_i) \big) = \operatorname*{argmin}_{x} \sum_i \parallel f_i(x_i) \parallel^{2}_{{\Sigma}_i}.
   :label: factor_graph_map_log

The MAP inference problem in Eq. :eq:`factor_graph_map_log` is converted to the same non-linear least squares optimization problem in Eq. :eq:`ls`, which can be solved following the same steps in section before.

There are several advantages of using factor graph to model the non-linear least squares problem in SLAM.
Factor graphs encode the probabilistic nature of the problem, and easily visualize the underlying sparsity of most SLAM problems since for most (if not all) factors :math:`x_i` are very small sets.

.. rubric:: References

.. [1] Dellaert, F., & Kaess, M. (2006). Square Root SAM: Simultaneous localization and mapping via square root information smoothing. The International Journal of Robotics Research, 25(12), 1181-1203.