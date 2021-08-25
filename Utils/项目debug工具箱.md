#### FastDebugTools

```kotlin
class DebugTools {

    fun buildVersion(): String {
        //构建版本 : 1.0.1
        return "构建版本:" + BuildConfig.VERSION_CODE + "." + BuildConfig.VERSION_NAME
    }

    fun buildTime(): String {
        //new date() 当前运行时拿到的时间，这个包 被打出来的时间
        return "构建时间:" + BuildConfig.BUILD_TIME
    }

    fun buildEnvironment(): String {
        return "构建环境: ${
            when (BuildConfig.DEBUG) {
                true -> {
                    "测试环境"
                }

                false -> {
                    "正式环境"
                }
            }
        }"
    }

    @FastDebug(name = "一键开启Https降级", desc = "将继承Http,可以使用抓包工具明文抓包")
    fun degrade2Http() {
        SPUtil.putBoolean("degrade_http", true)
        val context = AppGlobals.get()?.applicationContext ?: return
        //得到启动页的intent
        val intent =
            context.packageManager.getLaunchIntentForPackage(context.packageName)
        intent?.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK)
        context.startActivity(intent)

        //杀掉当前进程,并主动启动新的 启动页，以完成重启的动作
        Process.killProcess(Process.myPid())
    }
}
```