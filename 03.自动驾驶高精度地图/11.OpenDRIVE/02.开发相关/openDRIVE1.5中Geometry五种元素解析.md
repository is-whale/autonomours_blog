- [openDRIVE1.5中Geometry五种元素解析_WeakyTang的博客-CSDN博客](https://blog.csdn.net/tcytcy5525/article/details/105254603)

# openDRIVE1.5中Geometry五种元素解析

## Geometry

`geometry`是`planView`的子标签
关于`planView`这里不再赘述，本文主要解析geometry的5个子元素

```
<planView>
    <geometry>
        ...
    </geometry>
</planView>
```

这里有几个变量需要注意

> s: 起始位置(s-coordinate)
>
> x: 起始位置(x inertial)
>
> y: 起始位置(y inertial)
>
> hdg: 起始方向(inertial heading)
>
> length: 元素reference line的长度

在geometry中主要有以下五种道路表示的元素

- straight lines
- spirals
- arcs
- cubic polynomials
- parametric cubic polynomials

## straight lines

`line`是`geometry`的子标签

```
<geometry>
    <line.../>
    ...
</geometry>
```

是最简单的一种元素，将一条直线描述为道路参考线的一部分。

## spirals

`spiral`是`geometry`的子标签

```
<geometry>
    <spiral.../>
    ...
</geometry>
```

这里将螺旋线描述为道路参考线的一部分。
`spiral`的一个重要特点是，元素开始和结束之间的曲率变化是线性的。
关于spiral的详细计算，可以参考官方文档，这里做基本层面的介绍，不再赘述。

这里有几个变量需要注意

> curvStart: 元素起始位置的曲率
>
> curvEnd: 元素终止位置的曲率

## arcs

`arc`是`geometry`的子标签

```
<geometry>
    <arc.../>
    ...
</geometry>
```

这里将弧形描述为道路参考线的一部分。
`arc`的曲率参数curvature是恒定的。

这里有一个变量需要注意

> curvature: arc恒定不变的曲率

## cubic polynomials

`poly3`是`geometry`的子标签

```
<geometry>
    <poly3.../>
    ...
</geometry>
```

这里将三次[多项式](https://so.csdn.net/so/search?q=多项式&spm=1001.2101.3001.7020)描述为道路参考线的一部分，起点的局部u / v坐标系中描述多项式（u指向局部s方向，v指向局部t方向）。

通过简单地平移、旋转，可将u/v坐标系转到x/y坐标系下

> vlocal = a + b*du + c*du2 + d*du3

这里有几个变量需要注意

> a, b, c, d: 即上式中的方程参数

## parametric cubic polynomials

`paramPoly3`是`geometry`的子标签

```
<geometry>
    <paramPoly3.../>
    ...
</geometry>
```

这里参数三次曲线描述为局部u / v坐标系中道路参考线的一部分。 该曲线由两个三阶多项式组成。

通过简单地平移、旋转，可将u/v坐标系转到x/y坐标系下

> ulocal = au + bu*p + cu*p2 + du*p3
>
> vlocal = av + bv*p + cv*p2 + dv*p3

这里有几个变量需要注意

> aU, bU, cU, dU, aV, bV, cV, dV: 即上式中的方程参数
>
> prange: 该变量需要特别注意，是个string变量，表示参数p的范围

```
NOTE: 关于（参数）三次多项式的a, b, aU, aV, bV一般为0，若不为0则有特殊要求，详情可见官方文档
```