### 1：BIO

​    在JDK1.4出来之前，我们建立网络连接的时候采用BIO模式，需要先在服务端启动一个ServerSocket，然后在客户端启动Socket来对服务端进行通信，默认情况下服务端需要对每个请求建立一堆线程等待请求，而客户端发送请求后，先咨询服务端是否有线程相应，如果没有则会一直等待或者遭到拒绝请求，如果有的话，客户端会线程会等待请求结束后才继续执行

### 2：AIO

当进行读写操作时，只须直接调用API的read或write方法即可。这两种方法均为异步的，对于读操作而言，当有流可读取时，操作系统会将可读的流传入read方法的缓冲区，并通知应用程序；对于写操作而言，当操作系统将write方法传递的流写入完毕时，操作系统主动通知应用程序。  即可以理解为，read/write方法都是异步的，完成后会主动调用回调函数



### BIO、NIO 和 AIO 的 区别

Java BIO ： 同步阻塞，客户端有连接请求时服务器端就需要启动一个线程进行处理，这个连接不做任何事情

Java NIO ： 同步非阻塞，客户端发送的连接请求都会**注册到多路复用器**上，多路复用器**轮询到连接有I/O请求时才启动一个线程进行处理**

Java AIO： 异步非阻塞，服务器实现模式为一个有效请求一个线程，**客户端的I/O请求都是由OS先完成了再通知服务器应用去启动线程进行处理**

