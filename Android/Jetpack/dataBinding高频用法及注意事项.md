### 什么是dataBinding

> 可以理解为dataBinding只是一种工具，它解决了View和数据之间的双向绑定。

### dataBinding的优势

> * **双向数据绑定** 数据发生改变后，自动通知UI刷新界面，不再需要人工绑定最新数据到View上。UI改变后也能同步给数据
> * **减少模板代码** 有了`dataBinding`，从此不用再写`findViewById`,`setOnClickListener`等枯燥生硬的代码，大大提高工作效率。从此抛弃Butterknife
> * **释放Activity/Fragment** 我们可以直接在xml布局文件中完成数据，时间绑定工作。`Activity`,`Fragment`让它更加只关注核心业务
> * **数据绑定空安全** 在xml中绑定数据它是空安全的,因为`dataBinding`在数据绑定上会自动装箱和空判断，所以大大减少了数据绑定带来的`NullpointException`的问题

### 如何引入dataBinding

> 每个使用dataBinding的模块都应该在build.gradle中添加如下配置
>
> ```groovy
> android{
>     ...
>         dataBinding{
>             enabled = true
>         }
> }
> ```
>
> 

### dataBinding如何使用

> * 在布局文件中，选中根布局的标签，按住`Alt + 回车键`，点击`Convert to data binding layout`,既可转换成`dataBinding`布局
>
> * 转换后的布局，最外层变成了`layout`标签，里面包裹了`data`标签和常规的布局元素。`data`元素用来声明在此布局中使用到的变量和变量类型，以及类引用。
>
> * 如果一个属性`XXX`，在该类中`setXXX`方法，才能使用dataBinding来绑定，比如`android:layout_width,android_layout_height`就不能使用databinding来绑定值,而`android:paddingLeft,android:textSize`都是可以的。
>
>   

