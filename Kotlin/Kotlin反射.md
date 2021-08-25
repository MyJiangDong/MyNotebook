#### Koltin反射的使用

##### 获取反射的方法

1. ```kotlin
   val cl : Class = ClassName::class.java
   ```

2. ```kotlin
   val aClass = Class.forName("PackageName+ClassName")
   ```

3. 获取到Class之后，便可以获取有参方法

   ```kotlin
   val methodName = cl.getDeclaredMethod(String name, Class<?>... parameterTypes)
   ```

4. invoke方法中传入的是上一步获取到的方法的实例对象和方法传入的实参

   ```kotlin
   val objectClase = cl.newInstance()
   methodName.invoke(objectClase,参数)
   ```

##### 示例

```kotlin

        //获取字节码文件中 方法 再取出其公有方法
        val classname = "com.Person"
        //寻找名称的类文件，加载进内存 产生class对象
        val cl = Class.forName(classname)

        //获取一个Person对象
        println("获取一个Person对象:")
        val obj = cl.newInstance()
        println()


        //1.获取 公有 无参方法 public void demo2.Person.public_prin()
        val Person_public_prin = cl.getMethod("public_prin", null)
        println("获取执行 public void demo2.Person.public_prin() :")
        Person_public_prin.invoke(obj, null)

        println()

        //2.获取 公有 有参方法 public void demo2.Person.public_show(java.lang.String,int)
        val Person_public_show = cl.getMethod(
            "public_show",
            String::class.java,
            Int::class.javaPrimitiveType
        )
        println("获取执行 public void demo2.Person.public_show(java.lang.String,int) :")
        Person_public_show.invoke(obj, "神奇的我", 12)

        println()

        //3.获取 私有 无参方法 private void demo2.Person.private_prin()
        val Person_private_prin = cl.getDeclaredMethod("private_prin", null)
        Person_private_prin.isAccessible = true
        println("获取执行 private void demo2.Person.private_prin() :")
        Person_private_prin.invoke(obj, null)

        println()

        //4.获取 私有 有参方法 private void demo2.Person.private_show(java.lang.String,int)
        val Person_private_show = cl.getDeclaredMethod(
            "private_show",
            String::class.java,
            Int::class.javaPrimitiveType
        )
        Person_private_show.isAccessible = true
        println("获取执行 private void demo2.Person.private_show(java.lang.String,int) :")
        Person_private_show.invoke(obj, "神奇的私有", 23)

        println()
```

