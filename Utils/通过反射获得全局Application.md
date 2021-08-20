### 通过反射获得全局Application

```kotlin
object AppGlobals {
    var application: Application? = null

    fun get(): Application? {
        if (application == null) {
            try {
                val activityThread = 
                Class.forName("android.app.ActivityThread")
                val currentApplication = 			         activityThread.getDeclaredMethod("currentApplication")              
                val currentActivityThread =
                    activityThread.getDeclaredMethod("currentActivityThread")
                val current = currentActivityThread.invoke(null as Any?)
                application = currentApplication.invoke(current) as Application?
            } catch (ex: Exception) {
                ex.printStackTrace()
            }

        }

        return application
    }
}
```



