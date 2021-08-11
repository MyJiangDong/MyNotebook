

### ARouter

#### kotlin

##### build:gradle(Module:xxx)配置如下

```groovy
plugins {
    ...
    id 'kotlin-kapt'
    ...
}

kapt {
    arguments {
        arg("AROUTER_MODULE_NAME", project.getName())
        arg("AROUTER_GENERATE_DOC", "enable")
    }
}

dependencies {
	...
    implementation("com.alibaba:arouter-api:$arouterVersion")
    kapt "com.alibaba:arouter-compiler:$arouterCompilerVersion"
}

```

##### build:gradle(Project:xxx)配置如下

```groovy
 ext {
        ...
        arouterVersion = '1.5.2'
        arouterCompilerVersion = '1.2.2'
        arouterRegisterVersion = '1.0.2'
    }

 dependencies {
        ...
        classpath "com.alibaba:arouter-register:$arouterRegisterVersion"
 }


```

#### Application初始化

```java
private fun initARoute() {
        if (BuildConfig.DEBUG) {           
            // 这两行必须写在init之前，否则这些配置在init过程中将无效
            // 打印日志
            ARouter.openLog()       
                // 开启调试模式(如果在InstantRun模式下运行，必须开启调试模式！线上版本需要关闭,否则有安全风险)
            ARouter.openDebug()         
        }
    
        ARouter.init(this) // 尽可能早，推荐在Application中初始化
    }
```

#### 基本使用

##### 基本跳转

```kotlin
 ARouter.getInstance().build(path).navigation()
```

##### 带参数跳转

```kotlin
ARouter.getInstance().build(path)
                      	 .withString(key, value)
                       	 .navigation()
```

##### 带参数跳转接收

```kotlin
//key 需要和传递的key保持一致 
@JvmField
@Autowired
var key: String? = null

class Activity {
    fun onCreate(){
        ...
        //自动装箱需要注册
         ARouter.getInstance().inject(this)
        ...
    }
}
```

#### 全局降级服务

```kotlin
//当路由的时候,目标页不存在,此时重定向到统一错误页
@Route(path = "/degrade/global/service")
open class DegradeServiceImpl : DegradeService {
    override fun init(context: Context?) {

    }

    override fun onLost(context: Context?, postcard: Postcard?) {
        //需要使用绿色通道 greenChannel()
        ARouter.getInstance().build("/degrade/global/activity")
       						 .greenChannel()
        					 .navigation()
    }
}
```



####  全局拦截界面

```kotlin
@Interceptor(name = "ARouter_interceptor", priority = 9)
open class ARouterInterceptor : IInterceptor {
    private var context: Context? = null

    override fun init(context: Context?) {
        this.context = context
    }

    /**
     * 运行在子线程中
     */
    override fun process(postcard: Postcard?, callback: InterceptorCallback?) {
        val flag = postcard!!.extra

        when {
            flag and (RouteFlag.FLAG_LOGIN) != 0 -> {
                //login
                callback!!.onInterrupt(RuntimeException("need login"))
                loginIntercept()
            }
            else -> {
                callback!!.onContinue(postcard)
            }
        }
    }

    private fun loginIntercept() {
        Handler(Looper.getMainLooper()).post {
            showToast("请先登录")
            ARouter.getInstance().build("/account/login").navigation()
        }
    }

    private fun showToast(message: String) {
        Handler(Looper.getMainLooper()).postDelayed({
            Toast.makeText(context, message, Toast.LENGTH_SHORT).show()
        }, 1000)
    }
}
```

```kotlin
//RouteFlag
public interface RouteFlag {
    int FLAG_LOGIN = 0x01;
    int FLAG_AUTHENTICATION = FLAG_LOGIN << 1;
    int FLAG_VIP = FLAG_AUTHENTICATION << 1;
}

//使用
@Route(path = "/account/login", extras = RouteFlag.FLAG_LOGIN)
class Activity(){
    ...
}

```

