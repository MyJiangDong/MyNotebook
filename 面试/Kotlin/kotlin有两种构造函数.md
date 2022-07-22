两种构造函数分别为：

* 主构造函数 `primary constructor`
* 次构造函数 `secondary constructors`

在 `Kotlin` 中的一个类可以有一个**主构造函数**以及一个或多个**次构造函数**

####  主构造函数（primary constructor）

主构造函数紧跟在类名之后，用constructor修饰，有如下几种写法

1. 标准写法

```kotlin
class Device constructor(deviceId: Int, deviceMac: String, deviceName: String) {
    
    private val deviceId = deviceId
    private val deviceMac = deviceMac
    private var deviceName = deviceName
}
```

一个含有三个成员变量的Device类。此时要实例化一个Device对象，在构造方法中的这三个参数必须传递，不支持无参构造（使用无参构造实例化对象会报编译错误）。 

2. 对标准写法的简化（省略constructor关键字）

```kotlin
class Device(deviceId: Int, deviceMac: String, deviceName: String) {
    private val deviceId = deviceId
    private val deviceMac = deviceMac
    private var deviceName = deviceName
}
```

大多数情况下，没有其他修饰符修饰主构造函数的话，主构造函数的constructor关键字是可以省略的。

3. 对标准写法的进一步简化

```kotlin
class Device(private val deviceId: Int, private val deviceMac: String, private val deviceName: String) {
 
}
```


将成员变量直接写到了主构造函数的形参中，四行的代码压缩成一行就能搞定，看到Kotlin的简洁之处了吧，虽然对于初学者乍一看有点闷逼，但是这样可以大大提高开发效率。

如果初学者对上面三种方法看的迷糊的话，我把它翻译成Java，对比着看会更加易懂。

**Java代码如下**

```java
public class Device {
   
    private int deviceId;
    private String deviceMac;
    private String deviceName;
 
    public Device(int deviceId, String deviceMac, String deviceName) {
        this.deviceId = deviceId;
        this.deviceMac = deviceMac;
        this.deviceName = deviceName;
    }
}
```
讲到这里细心的读者可能发现上述的三种写法可能还满足不了一些其他需求，比如要对构造方法中传入的参数进行一些处理，下面举个在Adapter中经常遇到的Java代码的例子：

```java
public class Adapter {
    
    private LayoutInflater inflater;
    private List<Object> data;
 
    public Adapter(Context context, List<Object> data) {
        this.inflater = LayoutInflater.from(context);
        this.data = data;
    }
}
```
这边并没有直接通过构造函数的参数对成员变量直接赋值，而是通过了LayoutInflater.from(context)方法的转换。然而Kotlin的主构造函数并没有代码块，如何支持以上操作呢？这里就引出了init关键字。

4. 使用init关键字初始化代码

```kotlin
class Adapter(context: Context?, data: List<Any>) {
 
    private val inflater: LayoutInflater
    private val data: List<Any> = data
    init {
        inflater = LayoutInflater.from(context)
    }
}
```

注意：以上代码是有序执行的。另外，init代码块和Java中的static的代码块完全是两码事，前者是在对象的实例化时调用，后者是类加载时调用。

其实上面的写法并不是最简的，这里只是为了引出init而举个例子方法读者理解，下面再对这段代码进一步简化。

```kotlin
class Adapter(context: Context?, data: List<Any>) {
 
    private val inflater: LayoutInflater = LayoutInflater.from(context)
    private val data: List<Any> = data
}
```

对于存在修饰符的主构造函数，constructor关键字不能被省略，比如声明主构造函数为私有方法（默认是pubic的）就不能省略constructor，否则会报编译错误。

```kotlin
class Device private constructor(private val deviceId: Int, private val deviceMac: String, private val deviceName: String) {
 
}
```

#### 次构造函数（secondary constructors）

首先看看次构造函数是如何实现如上述例子仅有一个构造函数的情况

1. 单个构造函数下的次构造函数

```kotlin
class Device {
 
    private val deviceId: Int
    private val deviceMac: String
    private var deviceName: String
 
    constructor(deviceId: Int, deviceMac: String, deviceName: String){
        this.deviceId = deviceId
        this.deviceMac = deviceMac
        this.deviceName = deviceName
    }
}
```

这里的代码就有点类似Java的实现了，可见在类中仅有一个构造函数的情况下推荐使用更简介的主构造函数。

2. 多个次构造函数重载

```kotlin
class Device {
 
    private val deviceId: Int
    private val deviceMac: String
    private lateinit var deviceName: String
 
    constructor(deviceId: Int, deviceMac: String, deviceName: String) {
        this.deviceId = deviceId
        this.deviceMac = deviceMac
        this.deviceName = deviceName
    }
    constructor(deviceId: Int, deviceMac: String){
        this.deviceId = deviceId
        this.deviceMac = deviceMac
    }
}
```

或者 

```kotlin
class Device {
 
    private val deviceId: Int
    private val deviceMac: String
    private lateinit var deviceName: String
 
    constructor(deviceId: Int, deviceMac: String, deviceName: String) : this(deviceId, deviceMac){
        this.deviceName = deviceName
    }
    constructor(deviceId: Int, deviceMac: String){
        this.deviceId = deviceId
        this.deviceMac = deviceMac
    }
}
```
两种方法的区别就是后者通过用this关键字做了一个委托，本质是一样的。可见，存在多个次构造函数情况下，使用this关键字做委托不是必须的，这点要和下面介绍的第三种情况做区分。

3. 主构造函数和次构造函数共存

```kotlin
class Device(val deviceId: Int) {
 
    private lateinit var deviceMac: String
    private lateinit var deviceName: String
 
    //this关键字直接委托给主构造函数
    constructor(deviceId: Int, deviceMac: String) : this(deviceId){
        this.deviceMac = deviceMac
    }
    
    //this关键字间接委托给主构造函数
    constructor(deviceId: Int, deviceMac: String, deviceName: String) : this(deviceId, deviceMac) {
        this.deviceName = deviceName
    }
}
```
如果类中声明了主构造函数，再声明次构造函数的时候必须使用this关键字对主构造函数进行直接或间接的委托。注意：即使没有主构造函数这样的委托也会隐式发生的，而且这种委托发生在次构造函数的第一条语句，因此类中的init代码块会优先执行。如果看不懂前面这几句话，那就先看下面的实验。

```kotlin
class Device {
 
    //初始化块中的代码实际上会成为主构造函数的一部分
    init {
        println("first init")
    }
 
    //this关键字隐式委托给主构造函数
    constructor(deviceId: Int, deviceMac: String){
        println("second init")
    }
 
    //this关键字间接隐式委托给主构造函数
    constructor(deviceId: Int, deviceMac: String, deviceName: String) : this(deviceId, deviceMac) {
        println("third init")
    }
 
    companion object {
        @JvmStatic
        fun main(args: Array<String>) {
            Device(123,"mac","name")
        }
    }
}
```
