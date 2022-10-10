# [Machine Learning] Basic Math for ML with Python

> Machine learning is nothing but a geometry problem ! All data is the same. Understanding geometry is very important to solve machine learning problems. 

## [Numpy] Basic Linear Algebra

`Linear algebra`, mathematical discipline that deals with **vectors** and **matrices** and, more generally, with **vector spaces** and **linear transformations**  ([Britannica](https://www.britannica.com/science/linear-algebra))


In this lecture, you will learn 

1. Scala, Vector, Array, Tensor 
2. Dot product & Norm
3. Multiplication & Transpose & Invertible matrix
4. Linear Transformation 
5. Eigen Value & Eigen Vector 
6. Cosine Similarity 


and perform these with `Numpy`

### 1. Scala, Vector, Matrix, Tensor 

#### Scala

- A Scalar has only magnitude (size) and is like a number 

```python
a = 1
b = 0.25
```

#### Vector

{{< figure src="vector.png" title="Vector" >}}

- A vector has *magnitude* and *direction* and is a list of numbers (can be in a row or column) which could present as $ \vec{p} = (1, 2, 3)$


```python
import numpy as np 

a = np.array([1, 2, 3])

b = np.array([2, -2.4, 0.5])
```

#### Matrix
- A matrix is an array of numbers

```python
import numpy as np 

a = np.array([[1, 2, 3], 
              [4, 5, 6]]) # 2 X 3 matrix

b = np.array([[2, -2.4, 0.5, 0.14], 
              [1, 0.3, 2, 1]]) # 2 X 4 matrix
```

#### Tensor
Assuming a basis of a real vector space, *e.g., a coordinate frame in the ambient space*,, a tensor can be represented as an organized **multidimensional array** of scalars (numerical values) with respect to this specific basis - [wikipedia](https://en.wikipedia.org/wiki/Tensor)


{{< figure src="tensor.png" title="Visualization of scalar, vector, array, and tensor" >}}


### 2. Dot product & Norm

#### Dot product

Dot product equals to inner product which is the sum of the products of corresponding components. Numpy helps us calculate vector's inner product easily with `dot()` function. 


{{< admonition type=note title="Inner Product Reference" open=false >}}


Check [Wolfram MathWorld page](https://mathworld.wolfram.com/InnerProduct.html) to get a deeper understanding of inner product! 

{{< /admonition >}}




```python 
a = np.array([1, 2, 3])
b = np.array([3, 2, 1])

print(np.dot(a,  b)) # inner product by using dot() 
print(np.sum(a * b)) # inner product by using summation of multiplication
```

The vector norm refers to the length of the vector. In machine learning, the most commonly used norms are $L^2$ Norm and $L^1$ Norm. 

* **$L^2$ Norm**

  $L^2$ is represented as $||\vec{x}||_2$.
  $$||\vec{x}||_2 = \sqrt{x_1^2 + x_2^2+ \cdot \cdot \cdot + x_n^2}  = \sqrt{\sum^n_k{x^2_k}}$$ where $k = 1...n$. 


* **$L^1$ Norm**

  $L^1$ is represented as $||\vec{x}||_1$.
  $$||\vec{x}||_1 = |x_1| + |x_2| + ... + |x_n| = \sum^n_k{|x_k|}$$. 

In Numpy, Norm can be calculated by using **linalg.norm()** function. 

```python 
a = np.array([1, 1, -2, -2])

# L2 Norm (default)
np.linalg.norm(a) 

# L1 Norm 
np.linalg.norm(a, 1)
```
### 3. Multiplication & Transpose & Invertible matrix

#### Multiplication

Numpy's `dot()` function can be used for matrix multiplication 

```python 
# a = 2 X 3
a = np.array([[1, 2, 3], 
            [1, 2, 3]])
# b = 3 X 2 
b = np.array([[1, 2],
            [3, 4], 
            [5, 6]])

print(np.dot(a, b)) # 2 x 2

```   

#### Transpose

The transpose of a matrix is simply a flipped version of the original matrix. We can transpose a matrix by switching its rows with its columns. 

In numpy, implementing matrix transpose can be done by just adding `.T` at the end of the original matrix 

```python 
a = np.array([[1, 2, 3], 
            [1, 2, 3]])

print(a.T) # Transpose of a matrix A
```

Multiplying  matrix A and transpose of a matrix A can be implemented as following:
```python 
a = np.array([[1, 2, 3], 
            [1, 2, 3]])

print(np.dot(a, a.T)) 
```
#### Invertible matrix

In linear algebra, an n-by-n square matrix is called invertible, if the product of the matrix and its inverse is the identity matrix ([CUEMATH](https://www.cuemath.com/algebra/invertible-matrix/))

$$ AB = BA = I_n$$
$$\implies B = A^{-1}$$

where, 
* A is (n x n) invertible matrix
* B is (n x n) matrix called inverse of A
* $I_n$ is (n x n) identity matrix

```python 

a = np.array([[1, 2], 
            [3, 4]])
print(np.linalg.det(a)) # case when Determinant (det(A)) is not zero -> invertible matrix exists
b = np.array([[1, 2], 
            [1, 2]])
print(np.linalg.det(b)) # case when Determinant (det(B)) is zero -> invertible matrix doesn't exist

```

* Implement Invertible matrix
```python 
a = np.array([[1, 2], 
            [3, 4]])

print(np.linalg.inv(a))
```

### 4. Linear Transformation 

Linear Transformations are often used in machine learning applications. 


### 5. Eigen Value & Eigen Vector 
### 6. Cosine Similarity 
