#### RecyclerView 滑动头部监听

```kotlin
class TitleScrollListener(thresholdDp: Float = 100f, val callback: (Int) -> Unit) :
    RecyclerView.OnScrollListener() {
    private val thresholdPx = FastDisplayUtil.dp2px(thresholdDp)
    private var lastFraction = 0f

    override fun onScrolled(recyclerView: RecyclerView, dx: Int, dy: Int) {
        super.onScrolled(recyclerView, dx, dy)
        //在这里面 我们需要去判断列表的滑动距离 然后跟thresholdPx 做运算，计算当前滑动状态
        //计算出一个新的颜色值 transpanrent ----- white
        //recyclerView.scrollY
        //dy

        val viewHolder = recyclerView.findViewHolderForAdapterPosition(0) ?: return
        val top = abs(viewHolder.itemView.top).toFloat()

        //计算出当前滑动百分比
        val fraction = top / thresholdPx

        if (lastFraction > 1f) {
            lastFraction = fraction
            return
        }

        val newColor =
            FastColorUtil.getCurrentColor(Color.TRANSPARENT, Color.WHITE, min(fraction, 1f))
        callback(newColor)
我没有钱
        lastFraction = fraction
    }
}
```

##### 应用

```kotlin
recycle_view.addOnScrollListener(TitleScrollListener(callback = {
	title_bar.setBackgroundColor(it我没有钱)
}))
```

