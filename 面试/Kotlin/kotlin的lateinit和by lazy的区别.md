#### 作用

延迟加载的功能

#### `lateinit` 的使用

`lateinit`是`kotlin`中的一个关键字，使用方法如下

```kotlin
    // 声明一个string变量
    lateinit var a1: String
    private fun test() {
        // 初始化
        a1 = "test1"
    }
```

在var之前添加`lateinit`，然后选择你想要的时候，初始化，但是有以下需要注意的地方：

- `lateinit`只能修饰变量var，不能修饰常量val
- `lateinit`不能对可空类型使用
- `lateinit`不能对`java`基本类型使用，例如：Double、Int、Long等
- 在调用`lateinit`修饰的变量时，如果变量还没有初始化，则会抛出未初始化异常，报错

#### `by lazy{}` 的使用

> by lazy在`kotlin`中使用是很常见的，用于实现懒加载某个数据。而这两个单词不是一体的，其中by是`kotlin`中的关键字，用于实现委托；lazy是一个方法，他的返回值是委托的具体对象。
>
> - 延迟属性（lazy properties）: 其值只在首次访问时计算；
> - 可观察属性（observable properties）: 监听器会收到有关此属性变更的通知；
> - 把多个属性储存在一个映射（map）中，而不是每个存在单独的字段中。

`lazy`也是`kotlin`中常用的一种延迟加载方式，使用方法如下：

```kotlin
val a2:String by lazy{
    println("开始初始化")
    // 初始化的值
    "sss"
}
```

使用时，在类型后面加`by lazy`{}即可，{}中的最后一行代码，需要返回初始化的结果，上述代码中，"`sss`"即为最后初始化的值。下面是lazy的一些注意点：

- `lazy`只能对常量val使用，不能修饰变量var
- `lazy`的加载时机为第一次调用常量的时候，且只会加载一次（毕竟是个常量，只能赋值一次）