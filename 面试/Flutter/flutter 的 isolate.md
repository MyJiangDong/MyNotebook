#### Flutter的isolate

Flutter是使用Dart语言为基础，所以它的线程机制使用的也是Dart的线程机制，Dart为我们提供了isolate，isolate跟线程差不多，它可以理解为Dart线程。isolate与线程的区别就是线程与线程之间是共享内存的，而isolate和isolate之间是不共享的，所以叫isolate（隔离）



#### isolate的定义

isolate本身的意思是"隔离",因为isolate之间的内存在逻辑上是隔离的。isolate中的代码是按顺序执行的，任何Dart程序的并发都是运行多个isolate的结果。因为Dart没有共享内存的并发，没有竞争的可能性所以不需要锁，也就不用担心死锁的问题。

