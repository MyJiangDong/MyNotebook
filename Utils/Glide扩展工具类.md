#### Glide扩展工具类

```kotlin
/**
 * 基本加载
 */
fun ImageView.loadUrl(url: String) {
    Glide.with(this).load(url).into(this)
}

/**
 * 加载圆形图片
 */
fun ImageView.loadCircle(url: String) {
    Glide.with(this).load(url).transform(CenterCrop()).into(this)
}

/**
 * 加载圆角图片
 */
fun ImageView.loadCorner(url: String, corner: Int) {
    Glide.with(this).load(url).transform(CenterCrop(), RoundedCorners(corner)).into(this)
}

/**
 * 加载圆形并且带边框的图片
 */
fun ImageView.loadCircleBorder(
    url: String,
    borderWidth: Float = 0f,
    borderColor: Int = Color.WHITE
) {
    Glide.with(this).load(url).transform(CircleBorderTransform(borderWidth, borderColor)).into(this)
}

/**
 * 自定义圆形带边框的transform
 */
class CircleBorderTransform(private val borderWidth: Float, private val borderColor: Int) :
    CircleCrop() {
    private var borderPaint: Paint = Paint(Paint.ANTI_ALIAS_FLAG)

    init {
        borderPaint.apply {
            color = borderColor
            style = Paint.Style.STROKE
            strokeWidth = borderWidth
        }
    }

    override fun transform(
        pool: BitmapPool,
        toTransform: Bitmap,
        outWidth: Int,
        outHeight: Int
    ): Bitmap {
        val transform = super.transform(pool, toTransform, outWidth, outHeight)
        val canvas = Canvas(transform)

        val halfWidth = outWidth / 2f
        val halfHeight = outHeight / 2f

        canvas.drawCircle(
            halfWidth,
            halfHeight,
            min(halfWidth, halfHeight) - borderWidth / 2,
            borderPaint
        )

        canvas.setBitmap(null)

        return transform
    }
}
```