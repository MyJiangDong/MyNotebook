* `Buffer` 缓冲区：用于数据处理的基础单元，客户端发送与接收数据都通过Buffer转发进行
* `Channel`通道：类似于流；但，不同于IN/OUT Stream；流具有独占性与单向性；通道则偏向于数据的流通多样性
* `Selectors` 选择器：处理客户端所有事件的分发器

#### `Charset`扩展部分

* `Charset` 字符编码：加密、解密
* 原生支持的、数据通道级别的数据处理方式，可以用于数据传输级别的数据加密、解密等操作

#### `NIO-Buffer`

* `ByteBuffer,CharBuffer,ShortBuffer,IntBuffer,LongBuffer,FloatBuffer,DoubleBuffer`

* 与传统不同，写数据时先写到Buffer->Channel;读则反之
* 为NIO块状操作提供基础，数据都按"块"进行传输
* 一个Buffer代表一"块"数据

#### `NI0-Channel`

* 可从通道中获取数据也可输出数据到通道；按"块"Buffer进行
* 可并发可异步读写数据
* 读数据时读取到Buffer,写数据则必须通过Buffer写数据
* 包括：`FileChannel`、`SocketChannel`、`DatagramChannel`、等