# the-chat-room
socket编程，多人聊天室  

   一开始认为这个项目很简单，创立一个聊天室本质上也就是实现消息的发送与接受而已。所以写一个服务端与一个客户端，让两者间传输消息不就行了嘛。要实现这个很简单，但在写完进行测试的时候我便发现了不少问题：首先，由于是在服务端和客户端之间传递消息，所以必然只能实现两个用户之间两天，而我希望它不仅能实现两人聊天，也能实现多人聊天；此外，我所写的最初版本是没有使用多线程的，因此它不能并行的接受与发送消息，也就是说，此聊天系统只能允许“一人一句”的形式进行聊天。    
   为了解决此问题，我对原有聊天系统的框架进行了修改。首先，不再采用用户既是客户端又是服务端的模式，用户均为客户端，客户端可以有多个，客户端将用户输入的消息发送到服务端，而服务端会对接受到的信息进行一定的处理，然后发送给所有客户端。客户端与服务端均采用多线程模式以便并行的接受与发送消息。这样便可实现多人的线上聊天。  
    此聊天系统服务端发送的不仅有用户的聊天内容，还有用户列表，以此用户可以知道当前群聊有哪些人。  
    如图，这是服务端对于聊天服务的实现：  
 ![image]https://github.com/PaulDir/the-chat-room/blob/master/picture/1.png  
通过继承threading.Thread类而实现多线程，为此需重写run:  
 ![image]https://github.com/PaulDir/the-chat-room/blob/master/picture/2.png  
实现聊天服务首先需要能接受服务端的消息，如下，服务端首先会接收到用户端发送的用户名，如果用户名为空，使用用户的IP与端口作为用户名。如果用户名出现重复，为后出现的用户名依此加上后缀“_2”、“_3”、“_4”……  
 ![image]https://github.com/PaulDir/the-chat-room/blob/master/picture/3.png  
在获取用户名后便会不断地接受用户端发来的消息（即聊天内容），结束后关闭连接：  
 ![image]https://github.com/PaulDir/the-chat-room/blob/master/picture/4.png
如果用户断开连接，将该用户从用户列表中删除，然后更新用户列表。
 ![image]https://github.com/PaulDir/the-chat-room/blob/master/picture/5.png  
将地址与数据（需发送给客户端）存入messages队列。  
 ![image]https://github.com/PaulDir/the-chat-room/blob/master/picture/6.png  
服务端在接受到数据后，会对其进行一些处理然后发送给客户端，如下图，对于聊天内容，服务端直接发送给客户端，而对于用户列表，便由json.dumps处理后发送。  
 ![image]https://github.com/PaulDir/the-chat-room/blob/master/picture/7.png  
客户端方面，它会先后发送用户名与聊天内容：  
 ![image]https://github.com/PaulDir/the-chat-room/blob/master/picture/8.png  
  ![image]https://github.com/PaulDir/the-chat-room/blob/master/picture/9.png  

要成为一个完整的聊天系统，它还需能接受到服务端发送的消息：  
 ![image]https://github.com/PaulDir/the-chat-room/blob/master/picture/10.png  

他会对接收到的消息进行判断，如果是在线用户列表（用json.dumps处理过），便清空在线用户列表框，并将此列表输出在在线用户列表框中。  
 ![image]https://github.com/PaulDir/the-chat-room/blob/master/picture/11.png  
如果是聊天内容，便将其输出在聊天内容显示框中。  
 ![image]https://github.com/PaulDir/the-chat-room/blob/master/picture/12.png  

	UI通过tkinter库实现，首先是登录界面：  
 ![image]https://github.com/PaulDir/the-chat-room/blob/master/picture/13.png  
	登录界面将获取用户的IP地址（默认为127.0.0.1：65432）与用户名，若不输入用户名，会将用户IP作为用户名，同时输出警告：  
 ![image]https://github.com/PaulDir/the-chat-room/blob/master/picture/14.png  
  
登入之后便是聊天界面：  
 ![image]https://github.com/PaulDir/the-chat-room/blob/master/picture/15.png  
	如图，左上的文本框为显示聊天内容，黑色字体为他人聊天内容，绿色字体为自己发表的内容。右上文本框显示在线用户，如图，此时聊天室只有lnc与cnl两人。最下面的文本框为输入文本框，用户在此输入聊天内容并发送。  
	至此，一个初级的多人聊天系统便完成了。
