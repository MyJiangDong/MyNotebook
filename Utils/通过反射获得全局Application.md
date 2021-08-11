### 通过反射获得全局Application

```kotlin
object AppGlobals {
    var application: Application? = null

    fun get(): Application? {
        if (application == null) {
            try {
                application = Class.forName("android.app.ActivtiyThread")
                    .getMethod("currentApplication")
                    .invoke(null) as Application
            } catch (ex: Exception) {
                ex.printStackTrace()
            }

        }

        return application
    }
}
```



