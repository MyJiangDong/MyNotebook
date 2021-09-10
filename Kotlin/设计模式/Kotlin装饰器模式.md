### 1.创建抽象接口类

```kotlin
interface Anim{
    fun eat()
}
```



### 2.创建接口实现类

```kotlin
class Panda : Anim{
    override fun eat(){
        println("不知道吃什么")
    }
}
```



### 3.通过扩展函数实现装饰类

```kotlin
fun Panda.bamboo(decorator: () -> Unit){
    eat()
    println("可以吃竹子")
    decorator()
}

fun Panda.carrot(decorator: () -> Unit){
    println("可以吃胡萝卜")
    decorator()
}

fun main(){
    Panda.run{
        bamboo{
            carrot{}
        }
    }
}
```

