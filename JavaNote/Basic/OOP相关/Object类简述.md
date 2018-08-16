# Object类简述

## 一点概念

- 在 java 中 Object 类作为所有类的终极父类存在，所有类都继承于 Object 类
- 在不给出某类的具体父类的时候， java 自动将 Object 做为该类的父类
- 因为继承的原因，可以使用 Object 类型指代任意类型的对象 （ 向上自动转型 ）。但只可作为数据持有对象，处理数据还是要把对象转化成特定对象
- Object 类有一个默认的构造方法，在构造子类实例时，都会先调用这个默认构造方法

---

##  API 简介（11个）

- ### `equals( Object obj )`
```java
public boolean equals( Object obj )

    指示其他某个对象是否与此对象“相等”。
    参数：
        obj - 要与之比较的引用对象。
    返回：
        如果此对象与 obj 参数相同，则返回 true；否则返回 false。
```

equals 方法在非空对象引用上实现相等关系：
- 自反性：对于任何非空引用值 `x` ， `x.equals(x)` 都应返回 true。
- 对称性：对于任何非空引用值 x 和 y，当且仅当 `y.equals(x)` 返回 true 时，`x.equals(y)` 才应返回 true。
- 传递性：对于任何非空引用值 x、y 和 z，如果 `x.equals(y)` 返回 true，并且 `y.equals(z)` 返回 true，那么 `x.equals(z)` 应返回 true。
- 一致性：对于任何非空引用值 x 和 y，多次调用 `x.equals(y)` 始终返回 true 或始终返回 false，前提是对象中的信息没有被修改。
- 对于任何非空引用值  x ， `x.equals(null)` 都应返回 false。

对于任何非空引用值 x 和 y，当且仅当 x 和 y  *引用同一个对象* 时，此方法才返回 true （x == y 具有值 true）

#### Tips from API : 当此方法被重写时，通常有必要重写 hashCode 方法，以维护 hashCode 方法的常规协定，该协定声明相等对象必须具有相等的哈希码。

