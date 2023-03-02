---
layout: post
title: "Linear Algebra for Machine Learning"
permalink: "/linear-algebra-machine-learning/"
---

# Linear Algebra Foundations for Machine Learning
_Updated on 10/26/2019_

Linear algebra is a tool to help manage large data sets. It comes with techniques that makes manipulation of data very convenient.

However, it makes more sense when described using geometry. I will describe it using only a two dimensional plane for sake of visual clarity.

A 2-D plane means a data set with only two entries. But same ideas are application to n-dimensional plane or data sets with n entries.

Most of the heavy work in linear algebra can be handled using computers. But these fundamental ideas are key to understanding what is happening under the hood.

I drew in heavily from the excellent YouTube playlist called [The Essence of Linear Algebra](https://www.youtube.com/watch?v=fNk_zzaMoSs&list=PLZHQObOWTQDPD3MizzM2xVFitgF8hE_ab) from [3Blue1Brown](https://www.youtube.com/channel/UCYO_jab_esuFRV4b17AJtAw).

In this playlist, Grant Sanderson explains the ideas using simple geometric operations using lines and dots on paper, i.e., a two dimensional plane.

Here is what I learnt.

## Linear algebra concepts
Linear algebra involves writing numbers in columns (), called vectors and boxes (), called matrices.

### Vector shows a location
A vector, single column of numbers (3, 5) represents a point on a cartesian 2-D plane, or any n-D space for that matter.

You need two things to make this work-
1. Define a origin (0, 0) from where the vector points to the location of the point.
2. Define the units of direction in x and y axes.  

### The basis vectors
The unit vectors are called basis vectors. They tell you how far to move from the origin and in which direction. The basis vectors along the x-axis, y-axis and z-axis are called i, j and k respectively.

### Linear combination of vectors and span
Any point on the 2-D plane can be reached with sum of two or more vectors.

However, its not possible when two vectors are parallel. In that case, all possible linear combinations are on a straight line.

There is also a third possibility, when the two vectors are zeros, no linear combination will get you anywhere from zero.

The span of vectors v and w is the set of all their linear combinations.

In a 3-D space, a span of 2 vectors will most likely be a 2-D plane, or a line if they are parallel. A span of 3 vectors in the same plane will cover the entire 3-D space, or a plane, or a line, or, in the most extreme case, just the origin.

### Linear transformation of the plane
If you stretch, skew, rotate or invert the vector plane keeping-
* the same origin same, and
* grid lines parallel to one another,
then it is called a linear transformation of the plane.

The 2-D plane doesn't have to be with all square grids. We can define a plane where the axes are not perpendicular to one another.

Linear transformation lets us define a vector on one plane using basis vectors of another plane.

### Matrix
A matrix is just a row of vectors that defines a linear transformation. The first column tells where i lands, the second column defines where j lands, and so forth.

### Vector dot products
When you multiple two vectors using dot product, you get the area of the parallelogram enclosed by the vectors. For a 3-D plane, this will be the volume.

### Determinant
When you transform a plane using a matrix, for example, skew it. The area enclosed by vectors in that plane changes. Determinant tells you by how much the area changes.

When talking about a 3-D plane, the determinant shows the change of volume.

When the output of a transformation reduces the 2-D plane into a line, the determinant is zero.

### Rank
The number of dimensions of a the output of transformation is called rank.

When you apply a matrix on a 2-D plane and the output is a line, we call it has a rank of 1.

### Inverse matrices
An inverse matrix completes the transformation in reverse.

The inverse cannot be found if-
1. The rank of transformation < initial dimensions in the space (for example, you squish a 2-D plane into a line)
2. The determinant is zero

### Change of basis <Incomplete>
Matrices transform your vector space. In other words, if there is a vector space defined by your friend L,  vectors in your vector space.

How do you explain your vectors in terms of Victor's world?

Lets say matrix A transforms your basis to Victor's world. Therefore, A-1 does the opposite, it tells you where any vector in Victor's world lands in your world.

### Eigenvectors and eigenvalues
When you apply a matrix on a plane, the vectors change direction and are scaled. The vectors that remain on their own span, are called eigenvectors. The value by which these special vectors are scaled is called a eigenvalues.

For example, if the transformation is stretching 2x along the horizontal axis, the x-axis is an eigenvector with eigenvalue of 2.

Additionally, for a 3-D plane, the eigenvectors with eigenvalues of 1 are rotational axis.

Av = lambda*v, where v is eigenvector and lambda is the eigenvalue.

## Extending to abstract world of data
These ideas are explained using a 2-D, and sometimes, a 3-D plane. But you can (somewhat) easily extend it to n-D environment. Even abstract functions such as file compression or dimension reduction, when thought in light of geometric manipulation of space, starts making sense!

This is part of ongoing series on my machine learning training.

Please let me know what you think :)
