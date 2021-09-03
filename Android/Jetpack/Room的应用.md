#### FastStorage封装

1. 添加依赖

   ```groovy
   def room_version = "2.2.0-alpha01"
   implementation "androidx.room:room-runtime:$room_version"
   kapt "androidx.room:room-compiler:$room_version"
   ```

2. 需要缓存的数据结构千变万化,数据模型也不尽相同，所以需要一个通用的数据持久化方案

   ```kotlin
   @Entity(tableName = "cache")
   class Cache{
       @PrimaryKey(autoGenerate = false)
       @NonNull
       var key :String = ""
       //缓存数据的二进制
       var data : ByteArray? = null
   }
   ```

3. 持久化时,把object数据转换成二进制。body以及内嵌对象需要实现Serializable

   ```kotlin
   //序列化存储数据需要转换成二进制
   private fun<T> toByteArray(body: T): ByteArray{
       val baos = ByteArrayOutputStream()
       val oos = ObjectOutputStream(baos)
       oos.writeObject(body)
       oos.flush()
       return baos.toByteArray()
   }
   ```

4. 读取缓存,需要反序列化。可自动完成二进制到object的转换

   ```kotlin
   //反序列，把二进制数据转换成java object 对象
   private fun toObject(data : ByteArray?): Any?{
       val bais = ByteArrayInputStream(data)
   	val ois = ObjectInputStream(bais)
       return ois.readObject()
   }
   ```

5. 定义数据库操作对象

   ```kotlin
   @Dao
   interface CacheDao{
   	@Insert(onConflict = OnConflictStrategy.REPLACE)
       fun saveCache(cache: Cache):Long
       
       @Query("select *from cache where `key`=:key")
       fun getCache(key: String):Chche?
       
       @Delete
       fun deleteCache(cache: Cache):Int
   }
   ```

6. 定义缓存数据库CacheDatabase

   ```kotlin
   @Database(
   	//关联创建的数据表
       entities = [Cache::class]
   	//数据库版本
       version = 1
   )
   abstract class CacheDatabase : RoomDatabase(){
       companion object{
           private var database: CacheDatabase
           fun get(): CacheDatabase{
               return database
           }
           
           init{
               database = Room.databaseBuilder(
                   AppGlobals.get()!!,
                   CacheDatabase::class.java,
                   "fase_cache"
               ).build()
           }
       }
   }
   ```

   7.FastStorage封装

   ```kotlin
   object FastStorage {
       fun <T> saveCache(key: String, body: T) {
           val cache = Cache()
           cache.key = key
           cache.data = toByteArray(body)
           CacheDatabase.get().cacheDao.saveCache(cache)
       }
   
       fun <T> getCache(key: String): T? {
           val cache = CacheDatabase.get().cacheDao.getCache(key)
           return (if (cache?.data != null) {
               toObject(cache.data!!)
           } else null) as T
       }
   
       fun deleteCache(key: String) {
           val cache = Cache()
           cache.key = key
           CacheDatabase.get().cacheDao.deleteCache(cache)
       }
   
       private fun <T> toByteArray(body: T): ByteArray? {
           var baos: ByteArrayOutputStream? = null
           var oos: ObjectOutputStream? = null
   
           try {
               baos = ByteArrayOutputStream()
               oos = ObjectOutputStream(baos)
               oos.writeObject(body)
               oos.flush()
               return baos.toByteArray()
           } catch (e: Exception) {
               e.printStackTrace()
           } finally {
               baos?.close()
               oos?.close()
           }
   
           return ByteArray(0)
       }
   
       private fun toObject(data: ByteArray): Any? {
           var bais: ByteArrayInputStream? = null
           var ois: ObjectInputStream? = null
   
           try {
               bais = ByteArrayInputStream(data)
               ois = ObjectInputStream(bais)
               return ois.readObject()
           } catch (e: Exception) {
               e.printStackTrace()
           } finally {
               bais?.close()
               ois?.close()
           }
           return null
       }
   }
   ```
   
   