
Optimizing Your Own Types
===========================================

Python and C++ code of this example can be found at `optimize_custom_type.py <https://github.com/dongjing3309/minisam/blob/master/examples/python/optimize_custom_type.py>`_ and `optimize_custom_type.cpp <https://github.com/dongjing3309/minisam/blob/master/examples/cpp/optimize_custom_type.cpp>`_ respectively.

............................................................................

miniSAM already has build-in support for optimizing various commonly used manifold types in C++ and Python, including :code:`Eigen::Vector` types in C++, :code:`numpy.array` in Python, and Lie groups :math:`SO(2)`, :math:`SE(2)`, :math:`SO(3)`, :math:`SE(3)` and :math:`Sim(3)`, which are commonly used in SLAM and robotics problems, provided by `Sophus library <https://github.com/strasdat/Sophus>`_ in both C++ and Python.

In this page we see how to customize manifold properties of any C++ or Python class for miniSAM.
The example is given by defining a miniSAM-optimizable 2D vector space :math:`\mathbb{R}^2` type. 

Python example
---------------------------

In Python this is done by defining manifold-related member functions, including

- :code:`dim(self)`, function returns local manifold dimensionality. For :math:`\mathbb{R}^2` the dimensionality is just 2.
- :code:`local(self, other)`, function returns local coordinate of :code:`other` defined at origin :code:`self`. For :math:`\mathbb{R}^2` since the local manifold is itself, this function returns :code:`other - self`.
- :code:`retract(self, vec)`, given local coordinate :code:`vec` defined at origin :code:`self`, this function returns point projected on manifold of :code:`vec`. For :math:`\mathbb{R}^2` since the local manifold is itself, this function returns :code:`self + vec`.
- :code:`__repr__(self)`, function print :code:`self` to a string.

The example code:

.. code:: python

   # 2D point optimizable in miniSAM
   class Point2D(object):
       # constructor
       def __init__(self, x, y):
           self.x_ = float(x)
           self.y_ = float(y)

       # print function
       def __repr__(self):
           return 'custom 2D point [' + str(self.x_) + ', ' + str(self.y_) + ']\''

       # local coordinate dimension
       def dim(self):
           return 2

       # map manifold point other to local coordinate
       def local(self, other):
           return np.array([other.x_ - self.x_, other.y_ - self.y_], dtype=np.float)

       # apply changes in local coordinate to manifold, \oplus operator
       def retract(self, vec):
           return Point2D(self.x_ + vec[0], self.y_ + vec[1])


C++ example
---------------------------

In C++ we use a non-intrusive technique called *traits*, which is a specialization of template :code:`minisam::traits<>` for the type we are adding manifold properties. 

Using traits to define manifold properties has two major advantages: 

- Optimizing a class without modifying it, or even without knowing details of implementation (e.g. optimization support for third-party C/C++ types).
- Making optimizing primitive types (like :code:`double`) possible.

The specialization of template :code:`minisam::traits<>` needs following typedefs/static functions defined.

- typedef :code:`manifold_tag`, use :code:`manifold_tag` for regular manifold types and :code:`lie_group_tag` for a Lie group types.
- typedef :code:`TangentVector`, fixed or dynamic size :code:`Eigen::Vector` which has size of manifold dimensionality.
- :code:`static Dim()`, function returns local manifold dimensionality.
- :code:`static Local()`, function returns local coordinate of input defined at origin.
- :code:`static Retract()`, given local coordinate input defined at origin, this function returns point projected on manifold. 
- :code:`static Print()`, function to print to :code:`std::ostream`.

Given a C++ 2D vector space class :code:`Point2D`

.. code:: c++

   class Point2D {
    private:
     double x_, y_;

    public:
     // ctor
     Point2D(double x, double y) : x_(x), y_(y) {}

     // access
     double x() const { return x_; }
     double y() const { return y_; }
   };

The :code:`minisam::traits<Point2D>` specialization is defined by

.. code:: c++

   // speialization of minisam::traits<Point2D>
   namespace minisam {
   template <>
   struct traits<Point2D> {
     // minisam type category tag, used for static checking
     // must be manifold_tag or lie_group_tag to be optimizable
     typedef manifold_tag type_category;

     // print
     static void Print(const Point2D& m, std::ostream& out = std::cout) {
       out << "custom 2D point [" << m.x() << ", " << m.y() << "]'";
     }

     // tangent vector type defs
     typedef Eigen::Matrix<double, 2, 1> TangentVector;

     // local coordinate dimension
     static constexpr size_t Dim(const Point2D&) { return 2; }

     // map manifold point s to local coordinate
     static TangentVector Local(const Point2D& origin, const Point2D& s) {
       return Eigen::Matrix<double, 2, 1>(s.x() - origin.x(), s.y() - origin.y());
     }

     // apply changes in local coordinate to manifold, \oplus operator
     static Point2D Retract(const Point2D& origin, const TangentVector& v) {
       return Point2D(origin.x() + v[0], origin.y() + v[1]);
     }
   };
   }  // namespace minisam
