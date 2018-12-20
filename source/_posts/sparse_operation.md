---
title: Sparse matrix operation in scipy & tensorflow
date: 2018/12/20 21:14:00
updated: 2018/12/20 21:14:00
categories:
- python
- sparse matrix
- scipy.sparse
- tensorflow sparse tensor
---

scipy.sparse 提供了稀疏矩阵的操作，稀疏数据使用传统的存储方式总会有各种各样的缺点，而直接用sparse的方式进行存储和操作，使得代码编写便捷，并且大大提高了程序运行性能，本文旨在总结学习如何使用Python的scipy.sparse库与TensorFlow进行稀疏矩阵存储，运算等操作。

<!-- more -->

# scipy.sparse

1. coo_matrix 
   1. 是一种坐标形式的稀疏矩阵，也是常说的ijv或triple格式
   2. 初始化的几种方法:
      - coo_matrix(D) 
        - D为稠密矩阵
      - coo_matrix(S)
        -  S为另一个稀疏矩阵，也可以表示为：S.tocoo()
      - coo_matrix((M,N),[dtype]) 
        - construct an empty matrix with shape(M,N).dtype is an optional parameter with default dtype=d
      - coo_matrix((data,(i,j)) , [shape=(M,N)])
        - construct coo_matrix with 3 arrays
        - data[:]是值
        - i[:]是行的索引（坐标）
        - j[:]是列的索引（坐标）
    1. 稀疏矩阵的优点
       - sparse matrix supports arithmetic operations including addition, subtraction, multiplication, division, and matrix power(矩阵幂运算)
    2. coo格式的优缺点
       1. 优点
          1. faciliates fast conversion among sparse formats(促进各种格式稀疏矩阵之间的快速转换)
          2. permit duplicate entries(see examples)（coo_matrix 支持相同的坐标输入多次,支持相同的坐标下存放多个值）
          3. very fast conversion to and from CSR/CSC formats(可以快速转换到CSR/CSC格式，或从CSR/CSC格式转换回来)
       2. 缺点
          1. 不直接支持算术运算和切片
          2. 不支持元素插入或删除
    3. coo_matrix 格式使用目的
       1. coo is a fast format for constructing sparse matrices
       2. coo_matrix 被建好后，可以转换到CSR/CSC格式来进行快速运算和矩阵向量操作
       3. 当coo_matrix转换为CSR/CSC格式之后，duplicat(i,j)将会被summed together.有助于有效构造有限的矩阵
    4. coo_matrix 属性
       1. dtype
          - data的数据格式
       2. shape(2-tuple)
          - get the shape of matrix 
       3. nnz
          - number of stored values , including explicit zeros
       4. data
          - COO format data array  
       5. row
          - COO format row index array (x坐标数组)
       6. col  
          - COO format column index array （y坐标数组）
2. identity matrix in sparse format（稀疏单位矩阵）
   1. scipy.sparse.identity(n , dtype ='d' , format = None)
   2. 返回一个n*n的单位矩阵
   3. parameters
       1. n
          - 返回n*n矩阵，n为shape
       2. dtype
          - 数据格式
       3. format
          - CSR,DIA(斜对角)等格式
3. [csr_matrix](https://docs.scipy.org/doc/scipy-0.14.0/reference/generated/scipy.sparse.csr_matrix.html)
   1. 压缩行矩阵（compressed sparse row matrix）
   2. scipy.sparse.csr_matrix(arg1 , shape = None , dtype = None , copy = False)
   3. 实例化方式
      1. csr_matrix(D)
      2. csr_matrix(S)
      3. csr_matrix((M,N) , [dtype])
         - M*N的空矩阵 
      4. csr_matrix((data, ij) , [shape=(M,N)])
         -  与coo_matrix相似
      5. csr_matrix((data, indices, indptr), [shape = (M,N)])
         - data表示所有的非零元素的集合
         - indices表示所有非零元素在每行中的列下表
         - indptr[0,1,3,6]0后有几个数字表示有几行，数字代表行末的data 
      6. 优缺点
         1. 优点
            1. 高效的运算+，*等
            2. 行切片
            3. 快速的矩阵向量乘法
         2. 缺点
            1. 列切片慢
            2. 稀疏结构的改变代价高(使用LIL或DOK)
      7. 属性
         1. nnz
            - get the count of explicitly-sorted values 
         2. has_sorted_indices
            - determine whether the matrix has sorted indices 
         3. dtype
            - data type 
         4. shape
            - M*N 
         5. ndim
            - always 2(means M*N two dimention) 
         6. data
            -  CSR format data array
         7. indices
            - CSR format index array 
         8. indptr
            - CSR format index point array  
4. stack
   1. scipy.sparse.hstack
      1. stack sparse matrices horizontally (column wise)
      2. parameters
         1. blocks
            1. sequence of sparse matrices with compatible shapes
         2. format = None
         3. dtype = None
   2. scipy,sparse.vstack
      1. stack sparse matrices vertically (row wise)


# tensorflow sparse operation

1. tf.sparse.SparseTensor

2. tf.sparse.matmul