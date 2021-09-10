```kotlin
object FastRes {
    fun getString(@StringRes id: Int): String {
        return context().getString(id)
    }

    fun getString(@StringRes id: Int, vararg formatArgs: Any?): String {
        return context().getString(id, *formatArgs)
    }

    fun getColor(@ColorRes id: Int): Int {
        return ContextCompat.getColor(context(), id)
    }

    fun getDrawable(@DrawableRes id: Int): Drawable? {
        return ContextCompat.getDrawable(context(), id)
    }

    private fun context(): Context {
        return AppGlobals.get() as Context
    }
}
```

