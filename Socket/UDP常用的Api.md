#### DatagramSocket

* 用于接收与发送UDP的类

* 负责发送某一个UDP包，或者接收UDP包

* 不同于TCP，UDP并没有合并到Socket API中

  ```java
  DatagramSocket(); //创建简单实例，不指定端口与IP
  
  DatagramSocket(int port); //创建监听固定端口的实例，该端口是接收数据的端口
  
  DatagramSocket(int port,InetAddress localAddr);//创建固定端口指定IP的实例
  
  receive(DatagramPacket d);//接收
  
  send(DatagramPacket);//发送
  
  setSoTimeout(int timeout);//设置超时,毫秒
  
  close();//关闭、释放资源
  ```

#### DatagramPacket

* 用于处理报文

* 将byte数组、目标地址、目标端口等数据包装成报文或者将报文拆卸成byte数组

* 是UDP发送实体，也是接收实体

  ```java
  DatagramPacket(byte[] buf,int offset,int length,InetAddress address,int port);//前面3个参数指定buf的使用区间 后面2个参数指定目标机器地址与端口，用于发送
  
  DatagramPacket(byte[] buf,int length,SocketAddress address);
  //前面3个参数指定buf的使用区间 SocketAddress相当于InetAddress+Port
  
  setData(byte[] buf,int offset,int length);//指定buf的使用区间
  setData(byte[]);
  setLength(int length);
  
  getData();
  getOffset();
  getLength();
  
  setAddress(InetAddress iaddr);
  setPort(int iport);
  
  getAddress();
  getPort();
  
  setSocketAddress(SocketAddress address);
  getSocketAddress();
  ```

  

