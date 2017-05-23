title: java 网络编程（三）Socket
id: 1495183996052
author: 不识
tags:
  - java
  - 网络编程
categories:
  - java 基础
  - java 网络编程
date: 2017-05-19 16:53:00
---
URL和URLConnection为因特网提供了一个高水平的资源访问机制，但是有时我们需要一些低水平的网络通信，比如我们需要一个客户端-服务端程序。
这种客户端-服务端程序可能依据不同的需求，使用不同的协议，比如有的程序需要建立一种可靠的连接，以确保数据不会丢失，而有的则不需要。在网络模型中的传输层，提供给我们两种协议。
<!-- more -->

- **TCP** 传输控制协议（Transmission Control Protocol ）
- **UDP** 用户数据包协议（User Datagram Protocol）

TCP是一种可靠，面向连接的，且数据传输基于流的连接协议。使用TCP协议可以最大确保数据不会丢失，所以它使用于对可靠性要求较高的通信系统。应用层的Ftp,Http,Telnet,和Smtp都是基于TCP协议。

UDP则是一种不可靠，非连接，数据传输基于数据报的连接协议，它的特定是速度快，但不保证对方能够接收到数据，适用于那些对可靠性和连接要求不高的通信系统。应用层的Dns,Tftp,Dhcp,Nfs都是基于UDP协议.

无论是TCP还是UDP协议，都是点对点的通信连接，而要想在网络中寻找到指定的通信对象，就需要用IP地址和端口号作为其在网络中的唯一标记，而通过IP和端口号标记的每一个通信端，我们都称为一个Socket。两种协议都是通过socket进行相互的通信。


# TCP
java.net包中适用TCP协议的主要有两个类**ServerSocket**和**Socket**,分别代表服务端和客户端，在_java 网络编程（一）概述_中已经介绍过。   
下面是一个创建TCP连接，客户端向服务端上传文件的示例代码。首先是创建一个客户端Client,它读取本地一个文件upload.txt,然后发送给服务端。

```java
public class Client {
    public static void main(String[] args) throws IOException {

        try (Socket client = new Socket("localhost", 8888);
             BufferedInputStream input = new BufferedInputStream(new FileInputStream("C:\\upload.txt"));
             OutputStream out = client.getOutputStream()) {

            byte[] bytes = new byte[1024];
            int a;
            while ((a = input.read(bytes)) != -1) {
                out.write(bytes,0,a);
                out.flush();
            }

            client.shutdownOutput();//告诉服务端文件已经传输完毕
        }
    }
}

```

下面就是创建服务端类Server,接收Client传来的文件数据流，并将其保存到本地。

```java
public class Server {
    public static void main(String[] args) throws IOException {
        ServerSocket server = new ServerSocket(8888);
        try(Socket clientSocket = server.accept();
            BufferedOutputStream out = new BufferedOutputStream(new FileOutputStream("C:\\download.txt"));
            BufferedInputStream input = new BufferedInputStream(clientSocket.getInputStream())){
            
            byte[] bytes = new byte[1024];
            int a;
            while ((a = input.read(bytes)) != -1){
                out.write(bytes,0,a);
                out.flush();
            }
        }
    }
}

```
上面代码中都使用了try-with-resource来处理Socket和I/O流，所以不必我们自己来关闭。需要注意的是Server在使用getInputStream()获取网络中传输的文件I/O流时，无法获知是否到文件尾，所以要在Cilent中调用client.shutdownOutput()来告知已经到文件尾。

# UDP
java.net包中适用UDP协议的主要有两个类**DatagramPacket**和**DatagramSocket**,分别代表数据包和一个Socket，在_java 网络编程（一）概述_中已经介绍过，此外java.net还有一个MulticastSocket类，提供了组播功能，可以将数据包群发给多个接收端。 

下面是一个创建UDP连接,来模拟两个客户端发送消息的场景,首先是发送端接收来自控制台的标准输入信息，以数据包的形式发送给接收端,当输入over时，则停止接收输入，关闭发送端
```java
public class SendClient {
    public static void main(String[] args) throws IOException {
        try (DatagramSocket sendClient = new DatagramSocket();
             BufferedReader reader = new BufferedReader(new InputStreamReader(System.in))) {

            String s;
            while ((s = reader.readLine()) != null) {
                DatagramPacket packet = new DatagramPacket(s.getBytes(), s.getBytes().length, InetAddress.getByName("localhost"), 8888);
                sendClient.send(packet);

                if ("over".equals(s))
                    break;
            }
        }

    }
}

```
接着是一个接收端，接收发送端的数据包，并打印在控制台上，当接收发送端的over信息时，则停止监听接收数据包。

```java

public class RecevieClient {
    public static void main(String[] args) throws IOException {
        DatagramSocket recevieClient = new DatagramSocket(8888);

        while (true) {
            byte[] bytes = new byte[512];
            DatagramPacket packet = new DatagramPacket(bytes, bytes.length);
            recevieClient.receive(packet);
            String message = new String(packet.getData()).trim();

            if("over".equals(message))
                break;
            System.out.println(new String(packet.getData()));
        }
    }

}

```

如果我们要使用组播功能，需要将RecevieClient中的DatagramSocket换成MulticastSocket，在SendClient中我们指定DatagramPacket中指定了发送的地址“localhost”,这里是本机IP,但是在使用组播功能的时候，必须使用组播IP地址否则MulticastSocket将抛出“java.net.SocketException: Not a multicast address”。我们要想多个接收端接收此数据包，通过MulticastSocket.joinGroup(InetAddress)加入该组播IP群组即可。下面是组播的接收端实例代码。
```java
public class RecevieClient {
    public static void main(String[] args) throws IOException {
        MulticastSocket multiClient = new MulticastSocket(8888);
        //这里使用239.0.0.1组播地址，发送端同样需要修改发送的IP地址
        InetAddress group = InetAddress.getByName("239.0.0.1");
        //将该Socket加入此组播地址群组
        multiClient.joinGroup(group);

        while (true) {
            byte[] bytes = new byte[512];
            DatagramPacket packet = new DatagramPacket(bytes, bytes.length);
            multiClient.receive(packet);
            String message = new String(packet.getData()).trim();

            if("over".equals(message)){
                //结束时退出群组。
                multiClient.leaveGroup(group);
                break;
            }
            System.out.println(new String(packet.getData()));
        }
    }

}

```