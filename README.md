在生活中，我们常常需要用到随机数，有些是用于抽奖，有些是用于模拟，随机数的质量直接影响到算法的效果和系统的安全性。

然而，并非所有的随机数都是相同的，它们的生成方法和用途可以大相径庭。

本项目将深入探讨三种主流的随机数生成方法：线性同余方法、Inverse-Transform法以及Acceptance-Rejection法。每种方法都有其独特的应用场景和优缺点。通过对这些方法的详细分析，我们不仅能更好地理解随机数在现代计算中的重要性，还能够评估各种方法在实际应用中的适用性和效果。

# 线性同余法

公式：

$X_{n+1} = (aX_n + c) $% m  $

解释：

需要指定乘数 a , 加数 c, 被除数 m , 初始seed: $X_1$, 这里的 % 的意思是除以并取余数

举例子：

$X_1$ = 6 , a = 5 , c = 3 , m = 16

随机数1 = $X_1$ = 6

随机数2 = $(6*5+3)$%16=1

随机数3 = $(1*5+3)$%16=8

随机数4 = $(8*5+3)$%16=11

随机数5 = $(11*5+3)$%16=10

随机数 = (6,1,8,11,10...)

## 用线性余同法生成 Random Uniform variables

其余步骤相同，只是把生成的随机数再除以16就行了，以上面的例子就是：

随机数 = (0.375,0.062,0.5,0.687,0.625)

## 用R函数实现上述逻辑

```
Uniform_random = function(x1,a,c,m,n){
  v = numeric(n)
  for (i in 1:n) {
    v[i] = x1/m
    x1 = (x1*a+c) %% m
  }
  return(v)
}
```


# Inverse-Transform 方法

CDF是一个变量的在其取值X上的累计概率分布，CDF的y轴代表P(X<x)，也就是从这个分布中取值x,小于当前位置X的概率。

由于CDF的y轴代表的是概率，所以其取值总是介于(0,1)

![cdf](https://github.com/Tony980624/Random-Generating-Process/blob/main/file01/Rplot.png)

如果我们可以生成随机变量~Uniform(0,1)，然后带入CDF的y中去求X,是不是就能生成随机数了？

我们只需要准备一个随机变量的CDF的反函数和一个符合Uniform(0,1)的变量就好了.

```
inverse_transform = function(x1,a,c,m,n,mean,sd){
  v = numeric(n)
  w = numeric(n)
  for (i in 1:n) {
    v[i] = x1/m
    x1 = (x1*a+c) %% m
  }
  for (j in 1:n) {
    w[j] = qnorm(v[j],mean = mean,sd)
  }
  return(w)
}
```

# Acceptance-Rejection法


步骤1：选择一个随机分布，这里我选择的均值为0，标准差为5的正态分布：

![pdf](https://github.com/Tony980624/Random-Generating-Process/blob/main/file01/Rplot01.png)

步骤2；给这个分布的x和y设限制，这里我把x限定为(-12,12),y限定在(0,0.06)

![bpdf](https://github.com/Tony980624/Random-Generating-Process/blob/main/file01/Rplot02.png)

步骤3：在x的取值范围内生成一个服从均匀分布的变量,我这里生成了 -4.599556

```
runif(1,-12,12)
```

步骤4：在y的取值范围内生成一个服从均匀分布的变量,我这里生成了 0.01835325

```
runif(1,0,0.06)
```

步骤5：查看生成两个坐标形成的坐标的位置是否落在了pdf的下方？

