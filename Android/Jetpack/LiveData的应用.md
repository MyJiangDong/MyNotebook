#### FastDataBus

```kotlin
object FastDataBus {

    private val eventMap = ConcurrentHashMap<String, StickyLiveData<*>>()

    fun <T> with(eventName: String): StickyLiveData<T> {
        //基于事件名称, 订阅、分发消息
        //由于 一个 liveData 只能发送 一种数据类型
        //所以 不同的event事件,需要使用不同的liveData实例 去分发
        var liveData = eventMap[eventName]
        if (liveData == null) {
            liveData = StickyLiveData<T>(eventName)
            eventMap[eventName] = liveData
        }
        return liveData as StickyLiveData<T>
    }

    class StickyLiveData<T>(private val eventName: String) : LiveData<T>() {
        var mStickyData: T? = null
        var mVersion: Int = 0

        fun setStickyData(stickyData: T) {
            mStickyData = stickyData
            setValue(stickyData)
            //主线程发送
        }

        fun postStickyData(stickyData: T) {
            mStickyData = stickyData
            postValue(stickyData)
            //不受线程限制
        }

        override fun setValue(value: T) {
            mVersion++
            super.setValue(value)
        }

        override fun postValue(value: T) {
            mVersion++
            super.postValue(value)
        }

        override fun observe(owner: LifecycleOwner, observer: Observer<in T>) {
            super.observe(owner, observer)
        }

        fun observerSticky(owner: LifecycleOwner, sticky: Boolean, observer: Observer<in T>) {
            //允许指定注册的观察者 是否需要关心黏性事件
            //sticky = true,如果之前存在已经发送的数据，那么这个observer会受到之前的黏性事件消息
            owner.lifecycle.addObserver(LifecycleEventObserver { source, event ->
                //监听 宿主 发送销毁事件，主动把liveData 移除掉
                if (event == Lifecycle.Event.ON_DESTROY) {
                    eventMap.remove(eventName)
                }
            })

            super.observe(owner, StickyObserver(this, sticky, observer))
        }

    }

    class StickyObserver<T>(
        private val stickyLiveData: StickyLiveData<T>,
        private val sticky: Boolean,
        private val observer: Observer<in T>
    ) : Observer<T> {

        //lastVersion 和 liveData 的version 对齐,为了控制黏性事件的分发
        //sticky 不等于true,只能接收到注册之后发送的消息,如果要接收黏性事件，则sticky需要传递为true
        private var lastVersion = stickyLiveData.mVersion


        override fun onChanged(t: T) {
            if (lastVersion >= stickyLiveData.mVersion) {
                //就说明stickyLiveData 没有更新的数据需要发送
                if (sticky && stickyLiveData.mStickyData != null) {
                    observer.onChanged(stickyLiveData.mStickyData)
                }

                return
            }

            lastVersion = stickyLiveData.mVersion
            observer.onChanged(t)
        }


    }
}
```

#### FastDataBus使用

* 发送

```java
FastDataBus.INSTANCE.<String>with("Stack_Data").setStickyData("stickData from mainActivity");
```

* 接收

``` kotlin
 FastDataBus.with<String>("Stack_Data").observerSticky(this, false, {
            Toast.makeText(this, it, Toast.LENGTH_SHORT).show()
        })
```





