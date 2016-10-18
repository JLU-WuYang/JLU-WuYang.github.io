---
layout: post
title: 基于TCP的python聊天程序
description: "基于TCP的python聊天程序"
modified: 2016-10-19
tags: [Python]
image:
  feature: abstract-2.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---


### 基于TCP的python聊天程序

#### 基础知识：
- TCP/IP协议
- Python网络编程
- PythonGUI编程(Tkinter)

#### TCP/IP协议
TCP/IP不是一个协议，而是一个协议族的统称。里面包括了IP协议，IMCP协议，TCP协议，以及我们更加熟悉的http、ftp、pop3协议等等。

TCP协议与UDP协议定义在传输层，IP协议定义在网络层。

在之上的应用层定义HTTP协议等。

TCP连接包括三个状态：连接创建、数据传送和连接终止。操作系统将TCP连接抽象为套接字的编程接口给程序使用，并且要经历一系列的状态改变。

下面我们将会使用python的套接字模块进行编程。

#### Python网络编程
传统的TCP编程都是服务器和客户端模式。
客户端只能连接一个服务器，而服务器可以连接多个客户端。
所以，不难猜出对于服务器需要多线程进行处理，分别与多个客户端通信。

下面我们看第一个TCP编程的例子

- 服务器
```Python

#server_1.py
import socket


def tcplink(sock, addr):
    print 'Accept new connection from %s:%s...' % addr
    sock.send('Welcome!')
    while True:
        data = sock.recv(1024)
        time.sleep(1)
        if data == 'exit' or not data:
            break
        sock.send('Hello, %s!' % data)
    sock.close()
    print 'Connection from %s:%s closed.' % addr

host = socket.gethostname()
port = 12345
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.bind((host, port))
s.listen(5)
while True:
    # 接受一个新连接:
    sock, addr = s.accept()
    # 创建新线程来处理TCP连接:
    t = threading.Thread(target=tcplink, args=(sock, addr))
    t.start()

```
  
- 客户端

```python 
#cilent_1.py
import socket


host = socket.gethostname()
port = 12345
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
# 建立连接:
s.connect((host, port))
# 接收欢迎消息:
print s.recv(1024)
for data in ['Michael', 'Tracy', 'Sarah']:
    # 发送数据:
    s.send(data)
    print s.recv(1024)
s.send('exit')
s.close()
 ```
 执行结果如下：
 <figure>
	<img src="/images/client_1_server_1.png" alt="">
</figure>

 下面对上面代码进行简单的分析：
 
 通过socket模块进行python网络编程，相信学过C++网络编程的对于socket不陌生

 - **server.py**

   1. 通过socket.socket(args)进行创建socket对象，socket.AF_INET表示服务器之间网络通信，socket.SOCK_STREAM表示	
    流式socket , 用于 TCP（还有socket.SOCK_DGRAM是表示数据报式socket , 用于 UDP）
   2. s.bind(args)通过host（主机名/IP）和port（端口）确定绑定的地址
   3. s.listen(args)开始进行监听连接
   4. s.accept()接收客户端的连接，一旦接收连接，就返回客户端socket对象和地址用于通信
   5. 创建新线程进行处理，主线程仍然继续监听
   5. s.send()用来发送消息，s.recv()用于接收消息

- **client.py**
 
  1. 对于创建socket与服务器相同
  2. s.connect(args)用于连接某地址的服务器，连接上就可用send和recv来发送和接受消息了
  3. s.close() 用于关闭连接

下面我们尧根据需要对于上面代码进行改进，用于完成我们的聊天功能

下面命名为Server.py和Client.py，但是在客观上两者不存在服务器和客户端之分，应该均为客户端。

- 客户端一

```python
#Server.py
import socket


host = socket.gethostname()
port = 12345
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.bind((host, port))
s.listen(1)
sock, addr = s.accept()
print "Linked"
info = sock.recv(1024)
while info != "exit":
   print "From Others : "+info
    send_mes = raw_input("")
    sock.send(send_mes)
    if send_mes == "exit":
        break
    info = sock.recv(1024)
sock.close()
s.close()

```
   
- 客户端二

```python
#Client.py
import socket              


s = socket.socket()         
host = socket.gethostname() 
port = 12345               
s.connect((host, port))
print "Linked"
info = ""
while info != "exit":
    print "From Others: "+info
    send_mes = raw_input("")
    s.send(send_mes)
    if send_mes == "exit":
        break
    info = s.recv(1024)
s.close()  
```

执行结果如下：
 <figure>
	<img src="/images/Server_Client.png" alt="">
</figure>

简单解释一下代码逻辑：  

双方，一方进行监听，另一方进行连接。双方交互进行输入信息，并发送。当一方输入exit时，双方
的连接断开。程序结束。

而且，使用中会感到别扭，因为这种交流被代码固定。 
所以接下来我们将用GUI方式进行完善我们的聊天程序。

#### 思考与改进

尝试用  **客户端-服务器-客户端** 方式重写聊天程序



#### PythonGUI编程(Tkinter)

常用GUI库: 

- Tkinter
- wxPython
- Jython

今天我们用的是Tkinter

介绍：

Tkinter 是Python的标准GUI库。Python使用Tkinter可以快速的创建GUI应用程序。

先举个小例子感受一下：

```python
from Tkinter import *  


top = Tk()
top.mainloop()
```

 <figure>
	<img src="/images/example.png" alt="">
</figure>


因为GUI只是我们用来展示的部分，所以还是直接从代码中了解它

聊天程序所使用的GUI代码： 

```python
from Tkinter import *  
import socket

  
class Chat:  
    def __init__(self):  
        window = Tk()                 
        window.title("Chat")  
        self.text = Text(window)  
        self.text.pack()  
        frame1 = Frame(window)   
        frame1.pack()            
        label = Label(frame1,text="Enter your Message: ")     
        self.Message = StringVar()  
        entryMessage = Entry(frame1,textvariable=self.Message)  
        btSend = Button(frame1,text="Send",  
                           command=self.processSendButton)  
        btLink = Button(window,text="Link",  
                           command=self.processLinkButton) 
        btLink.pack()
        
        label.grid(row=1,column=1)  
        entryMessage.grid(row=1,column=2)  
        btSend.grid(row=1,column=4)  
        self.text.insert(END, "\t\t\t\t----------------\n\t\t\t\tWecolme to Chat \n\t\t\t\tEnjoy youself \n\t\t\t\t----------------\n\n\n")
        window.mainloop()  
      

  
      
    
    def processSendButton(self):  
        self.text.insert(END, "[You Message] : "+self.Message.get()+"\n")
    
    def processLinkButton(self):  
        pass
      
Chat()
```




关于程序的一些说明：

- 使用了Text，Label，Button，Entry控件
- 使用了Frame布局控件
- 第一类控件实例化时第一个参数为该控件的master，第二个参数为显示的内容，第三个为绑定的处理函数（当事件发生时调用的处理函数，也是业务逻辑所在的地方）


程序Chat-客户端一的代码：

```python
#Client_GUI.py
from Tkinter import *  
import socket, threading


def acceptMessage(sock, text):
    while True:
        text.insert(END, "[Other's Message] : "+sock.recv(1024)+"\n")
    
class Chat:  
    def __init__(self):  
        window = Tk()                 
        window.title("Chat")  
        self.text = Text(window)  
        self.text.pack()  
        frame1 = Frame(window)   
        frame1.pack()            
        label = Label(frame1,text="Enter your Message: ")   # 创建标签  
        self.Message = StringVar()  
        entryMessage = Entry(frame1,textvariable=self.Message)  
        btSend = Button(frame1,text="Send",  
                           command=self.processSendButton)  
        btLink = Button(window,text="Link",  
                           command=self.processLinkButton) 
        btLink.pack()
        
        label.grid(row=1,column=1)  
        entryMessage.grid(row=1,column=2)  
        btSend.grid(row=1,column=4)  
        self.text.insert(END, "\t\t\t\t----------------\n\t\t\t\tWecolme to Chat \n\t\t\t\tEnjoy youself \n\t\t\t\t----------------\n\n\n")
        window.mainloop()  
    
    def processSendButton(self): 
        self.s.send(self.Message.get())
        self.text.insert(END, "[You Message] : "+self.Message.get()+"\n")
    
    def processLinkButton(self):  
        self.s = socket.socket()         # 创建 socket 对象
        host = socket.gethostname() # 获取本地主机名
        port = 12345               # 设置端口好
        self.s.connect((host, port))
        self.text.insert(END, "Linked\n")
        t = threading.Thread(target=acceptMessage, args=(self.s, self.text,))
        t.start() 
Chat()
``` 


程序Chat-客户端二的代码：

```python
#Server_GUI.py
from Tkinter import *  
import socket, threading


def acceptMessage(s, text, theSystem):
    sock, addr=s.accept()
    theSystem.sock = sock
    while True:
        text.insert(END, "[Other's Message] : "+sock.recv(1024)+"\n")
    

  
class Chat:  
    def __init__(self):  
        self.s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        host = socket.gethostname() 
        port = 12345               
        self.s.bind((host, port))
        self.s.listen(1)
        window = Tk()                 
        window.title("Chat")  
        self.text = Text(window)  
        self.text.pack()  
        frame1 = Frame(window)   
        frame1.pack()            
        label = Label(frame1,text="Enter your Message: ")  
        self.Message = StringVar()  
        entryMessage = Entry(frame1,textvariable=self.Message)  
        btSend = Button(frame1,text="Send",  
                           command=self.processSendButton)  
        label.grid(row=1,column=1)  
        entryMessage.grid(row=1,column=2)  
        btSend.grid(row=1,column=4)  
        self.text.insert(END, "\t\t\t\t----------------\n\t\t\t\tWecolme to Chat \n\t\t\t\tEnjoy youself \n\t\t\t\t----------------\n\n\n")
        self.text.insert(END, "[NO PERSON] Wait for another one \n")
        t = threading.Thread(target=acceptMessage, args=(self.s, self.text, self))
        t.start()         
        window.mainloop()  
   
    def processSendButton(self):  
        self.sock.send(self.Message.get())
        self.text.insert(END, "[Your Message] : "+self.Message.get()+"\n")
    
Chat()
```

运行结果：

 <figure>
	<img src="/images/Server_GUI.png" alt="">
</figure>  

 <figure>
	<img src="/images/Client_GUI.png" alt="">
</figure>


