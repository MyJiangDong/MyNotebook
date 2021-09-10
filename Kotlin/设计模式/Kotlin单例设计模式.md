#### 饿汉式单例模式----空间换时间

```kotlin
object SingletonKotlin()
```

#### 懒汉式单例模式----时间换空间

```kotlin
//构造方法私有化
class SingletonKotlin private constructor(){
    companion object{
        private var instance: SingletonKotlin? =null
        	get(){
                if(field == null){
                    field = SingletonKotlin()
                }
                return field
            }
        
        //实现线程安全 并发效率低
        @Synchronized
        fun get(): SingletonKotlin{
            return instance!!
        }
    }
}
```

#### 双重校验锁单例模式

```kotlin
//构造方法私有化
class SingletonKotlin private constructor(){
    companion object{
        val instance: SingletonKotlin by lazy(mode = LazyThreadSafetyMode.SYNCHRONIZED){
            SingletionKotlin
        }
    }
}
```

#### 静态内部类单例模式

```kotlin
class SingletonKotlin{
    companion object{
        val instance = SingletonProvider.holder
    }
    
    private object SingletonProvider{
        val holder = SingletonK
    }
}
```

