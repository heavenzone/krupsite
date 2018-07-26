---
title: 用R语言对矩阵单位正交化
author: Heaven Zone
date: '2018-06-12'
slug: r-unit-orthogonalization
categories:
  - R
tags:
  - R
---

用R语言写了个函数对矩阵单位正交化。




```r
zj <- function(m) {
  a <- m
  b <- a * 0
  b[,1] <- a[,1]

  tmp.sum <- a * 0
  for(j in 2:ncol(a)) {
    tmp <- 0
    for(k in 1:(j-1)) {
      temp <- sum(a[,j]*b[,k])/sum(b[,k]*b[,k])*b[,k]
      tmp <- tmp + temp
    }
    b[, j] <- a[, j] - tmp
  }
  b
}

m_col_unit <- function(m) {
  rst <- m*0
  for(i in 1:ncol(m)){
    for(j in 1:nrow(m)) {
      rst[j, i] <- m[j, i]/sqrt(sum(m[, i]*m[, i]))
    }
  }
  rst
}

zj_unit <- function(m) {
  m <- zj(m)
  m_col_unit(m)
}

# 验证
tt <- matrix(c(1,1,0, 1,0,1, 0,1,1), nrow = 3)
zj(tt)
```

```{.code-output}
##      [,1] [,2]       [,3]
## [1,]    1  0.5 -0.6666667
## [2,]    1 -0.5  0.6666667
## [3,]    0  1.0  0.6666667
```

```r
zj_unit(tt)
```

```{.code-output}
##           [,1]       [,2]       [,3]
## [1,] 0.7071068  0.4082483 -0.5773503
## [2,] 0.7071068 -0.4082483  0.5773503
## [3,] 0.0000000  0.8164966  0.5773503
```

```r
m <- matrix(c(1,0,-1,1, 1,-1,0,1, -1,1,1,0), nrow = 4)
zj(m)
```

```{.code-output}
##      [,1]       [,2] [,3]
## [1,]    1  0.3333333 -0.2
## [2,]    0 -1.0000000  0.6
## [3,]   -1  0.6666667  0.6
## [4,]    1  0.3333333  0.8
```

```r
zj_unit(m)
```

```{.code-output}
##            [,1]       [,2]       [,3]
## [1,]  0.5773503  0.2581989 -0.1690309
## [2,]  0.0000000 -0.7745967  0.5070926
## [3,] -0.5773503  0.5163978  0.5070926
## [4,]  0.5773503  0.2581989  0.6761234
```
