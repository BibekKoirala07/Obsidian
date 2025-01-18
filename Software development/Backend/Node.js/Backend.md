### [[Best Practices]]
### [[Node]]
### [[ORMs & Mongoose]]
### [[Socket.io]]
### Beginning
![[Pasted image 20250102173919.png]]

### Event emitters in node.js
### Buffers
Buffers are the way to deal with zeroes and ones in node.js. To connect our technology which is node.js to connect to outside worlds 
To understand buffers we need to understand about binary number, hex numbers and character encodings.

Computer use binary however it shows hexadecimal because they are easy to see. Let's say we have 4000 characters in binary. then we will have just 1000 characters in hexadecimal.

==Absolutely learn Hexadecimal==. Because it is easy to learn and it is used in color code with &, URL with %, and also in special characters also.

Character Encoding
It is a system of mapping between character and numbers. It is a whole list. Two of the most popular character encoding are Unicode and ASCII. They are just standard. For ASCII, there are just 128 characters while Unicode has more than 1.5 lakhs. But always remember for some part of Computer only ASCII works. Like, in URL, if you try to type that is not in ASCII, it will convert to binary.
==And remember ASCII is just subset of Unicode.== the bigger question is that why is ASCII made if it is part of Unicode ? Well, ASCII can be represented in 8 bit only i.e. 2 hex only so For Unicode more are required. 

What is encoding/encoders and decoding/decoders ?
Well encoders takes something meaningful and convert that to zeroes and one. opposite is same for decoding. It is same with character encoding and character decoding. A system of assigning a sequence of bytes to a character. The most common one is UTF-8. therefore it has same numbers as Unicode which is defined by Unicode Standard. For UTF-8, U standard for Unicode, 8 stand for the fact that it will try to save the characters in eight bit sequences. Which means each character will be stored in 8 bit sequence or in 16 bit sequence or in 24 bit sequence or whatever. However for ASCII, it is always 8 bit sequence or 1 byte. Also the most significant bit in ASCII character is always zero. means `0 *******` That first bit is always zero in that ASCII binary representation.

The most important things is that let's say we encode some data using UTF-16 and decode using UTF-8, it will display meaningless data. so encoding and decoding should be done using same standard.

Now let's go to buffers.
It is a container in memory. We have to specify exact space of that buffer. Buffers acts like array however aren't array. just like array, buffers have elements and index also. After initializing, every bit is filled with 0 and buffer size is fixed And if we try to put more data than buffers, it will just discard it. In node.js, each element of buffers hold 8 bit and exactly 8 bits or 1 byte. At end, buffer is another data structure that is specially designed to work with binary data. Buffer is specially made for us to handle and in that memory we can do anything. We can  get data from somewhere, change that data, and send that data to somewhere. We can't allocate more memory that there exist, right ?

![[Pasted image 20250102155006.png]]
We can also write numbers in buffers but don't do that mostly as we already have methods to deal with that. for negative numbers it apply the signed and unsigned principle. The most important things is that we don't need to use buffer to store numbers. However buffers does provide methods to play with numbers.

For alloc and unsafealloc and unsafeallocSlow
We know that node.js instance have certain memory that is allocated for it, may be that is 8 kiB which it allocates for itself. That memory will be used for allocUnsafe only that to even in one condition(that is if the allocated memory is lower than half of that nodejs instance allocated memory). If we use alloc then that will not be used. and outside that node.js instance memory is used which is slow.
We also know that unsafealloc is faster but far less secure as it doesn't replace the bits with 0. However there is one thing to remember. However for unsafeAllocSlow, it doesn't use that memory of node.js instance at all. 
We also have things llike `Buffer.from` and `Buffer.concat`. But they use it alloc, unsafe and unsafeAllocSlow behind the scenes.

### Files
Everything is a file. In fact Every data belong to the file in hard drive. In fact whole OS is just collection of different file. And any OS can only execute executables only. In fact, it has no idea about any files if it isn't executables. Like in mac OS, it doesn't know how to use image. The application name Preview does it. So core OS doesn't know at all. So OS doesn't have any idea how to open the image file or pdf file. You have to ship that application to use that image or pdf or other files.

Node.js can't directly talk to hard drive. Rather it talks to OS and it uses system calls to talk with Hard drive. Node.js uses system calls to talk to your operating system. and only then OS goes to the hardware and do different things. Infact Node.js is using libuv system calls to talk to Operating System and get those files or write.
![[Pasted image 20250103130030.png]]

In Node.js We have 3 different way to do same thing in file system. They are Promises API, Callback API and Synchronous API. In terms of functionality they don't differ. In terms of uses, they are bit different. Mostly use Promises API, if you are doing extremely complex tasks, use callback API. Try not to use Synchronous API as possible. However for some tasks you might need it. Why ? cuz synchronous API uses ==main thread==. If you use this, then your whole node.js app will freeze when user is uploading file to node app. Mostly use ==Promises API==. For example let's say we need to print number from 1 to 1 million in some file. Callback version doesn't guarantee that those number will be in synchronous order. while Promises API does. So avoid callback as much as you can. Although it is faster, it takes around 10 times more memory which cause more bills. So don't use it that much. 

When we are trying to open the file, we aren't moving the whole content to file to memory rather just saving a number regarding that file. It is called file descriptor. It is just a number that is assigned to an opened file. Each opened file has unique descriptor.

Whenever we try to put string or number in file with `writeSync` or whatever, node.js convert it to buffer and then store it. Don't get deluded thinking that string is being putted.

Node.js doesn't understand image or video encoder. Rather install third party package.

### Streams
![[Pasted image 20250103163123.png]]
For making application faster, we need to know about streams.

While writing a data to streams, we need to use chunk. Chunk could be string or buffer. 

Streams are something that makes data streaming faster. Streaming just means flowing or continuous flow. Data streaming just means just that you don't move whole data in one chunk. You break it down in different chunk and send them to destination and combine them. One example is copying the file. Remember it uses chunk of 16kb or 1 mb or something else of chunk and send them to destination. It takes more time but isn't that much slower and far more secure. Let's say we have some video editing app, or photo editing app then node.js will get orders like cut from those parts or color those parts. What to do in that case ? Well, make a streams and pass from data. It is also used stream in http module.

In Node.js the size of chunks is 16kB. Everytime we would want to do data transfer with streams, node.js creates a stream and would wait for the data to gather and get big enough to be equal to the size of our chunk and only then we write.
![[Pasted image 20250103175532.png]]

Mainly there are four types of Stream. Readable, Writeable stream Duplex and Transform. And remember this is from node.js point of view. Like if node.js is sending to another process, then it is writable stream, and if data is being taken by node.js it is readable stream. If we are doing both then it is Duplex and Transform is whenever we want to transform the data Same with network card and storage.

![[Pasted image 20250104104726.png]]
Every Writable stream has some amount of internal buffer which by default is around 16 thousand bytes. Each stream objects also has some events properties and methods available. When we call `stream.write(data)`  , it push some amount of data to internal buffer. Remember, that data will always be buffer. If it is string, then node.js will use character encoding to change that to buffer. 

If we keep pushing, then internal buffer will be full and it now, stream will do write operation. Like it will pull all of this data out in one chunk and do one write. This is how writable stream operation works.
![[Pasted image 20250104105458.png]]
Why is this efficient ?
If want to write 1 million number, then it will write 1 million times to that files but if we use streams, it will collect the numbers until the internal buffer become full, then only will write. So, rather than writing 1 million times, it is collecting the data and isn't writing and will only write when stream object is full. So, no more million write. So it is efficient.

What if more than full capacity data size is pushed to internal buffer ? Like for last push which is of 2177 bytes in above figure ? Let's say we push 3000 bytes ? Then stream will push 2177 bytes in first phase. another 800 bytes will be in buffer. So, that 800 bytes will be in memory. Node.js will keep track of it. Once the internal buffer is empty, then only that extra 800 bytes will push for it. But that is problem ? What is we keep writing to this internal buffer without letting it to get emptied. Then node will keep buffering that extra bytes ? Well, that is the problem. let's say we have data of 800 MB. then that extra data will be in memory and will consume the memory. ![[Pasted image 20250104111711.png]]To mitigate the problem, we have to watch out for the event in which internal buffer does get empty. when stream does empty this buffer, it is called draining. Then only we can push code to internal buffer.

So For that 800 MB file, we should create a readable stream and it will create a chunk of 16kB. With this we will get the data and only then, we write the data to writable stream For that it to work.![[Pasted image 20250104114656.png]]
Duplex and Transform has two internal buffer

There are property in stream to know about the default size and property to know how much of this internal buffer is filled ? We can push to the internal buffer. What will happen if data pushed is equal to the wirelength of that internal buffer ? Well, we need to check right, if there is space for that or not ? if the space is full `stream.write(buff)` return false in boolean. So we need to check whether the stream is full or not. You should never write when stream is full. It will just increase a memory usage only. if that internal buffer is full and is emptied by writing, then `stream.on("drain)` event is executed. Only write extra things when that event occurs. 

TLDR is that streams have internal buffer with certain events, properties and what not. Don't push to internal buffer if that is full and wait for it to be empty and then only push the data. Everytime stream in written to file, it is called draining. It emit and event and event is emitted even when stream is ordered to end.

The `res` object in express is writable stream. And for more look at docs.

Now Readable stream ![[Pasted image 20250104112556.png]]
In readable stream, we get data from huge file and get them in chunks. For Readable Stream, the high Watermark is  65kb. We can read and end using `data` and `end`. Hard drive have higher read speed than write speed. And even in readable stream you should always use `drain` event to read. If you don't use it, it will use excessive memory. There is a option to pause the stream and resume it in readable stream. 

The benefit of stream is that it only use certain amount of memory like 30 MB, 50 MB etc. to even read or write 1 TB of file. if we don't use stream, our system will hang and it will cause computer to freeze. 

While reading the file there is a problem, let's say we would want to read from file which has from 1 to some billion. And remember file is read using stream with certain size of buffer(default is 65 kb). while reading let's say we would want to put those element in array. And let's say last element is 40,000 then what if we only get 40 and those 3 zeroes are in next buffer or array. What will happen  ? What to do ? We can check if the second last number add one is equal to the last number in array, if not rearrange in your own way. 

TLDR: In readable streams there is also `end` event which is fired when reading ends. It ends when `null` value is pushed in stream

==Rewatch Video 44==

`fs.copy` just use readable and writable stream to achieve the goal.

What we need to know is some function return return whole buffer of that file while some function return just buffers of standard size. You need to know this thing.

We need to use `.pipe()` so that node can take care of that draining and resuming operation. it also make co-ordination when source streams and destination streams are of different speed. If you don't use piping then we have do it manually. So try to use piping. it reduces the tedious work. However, There are some caveat. there is no good error handling code available in `.pipe`. so rather use `.pipeline()` as proper error handling and proper cleaning up also.  In `pipe()`, it doesn't do clean up which can make network use more memory and may cost more. at some time `npm i pump` was used to combat the issue but nodejs maintainer made `pipeline`

We can create a custom ReadableStream and custom writeAbleStream. Don't need to do it. just look at docs.

Now talking about Duplex and Transform.
![[Pasted image 20250106150026.png]]
In Readable Internal Buffer, the data(chunk) may remain in buffer or not. It is completely optional.
In node.js TCP sockets use Duplex.

![[Pasted image 20250106153649.png]]
We can create a Transform Stream using just by combining code of Readable and Writeable stream and doing some modifications. So, no , need to write the code. Just read the docs.

Creating Encryption and Decryption Application.
In Transform methods, methods like `_read`, `_write` and others are automatically done for you in Transform. So, rather use Duplex if you need to do that. rather just use `_transform` to make it work. So, make two files with two class and two files. And implement the required operation in `_transform`.

Until now, what we have seen is that, we have use Buffer in streams. But we can use js objects also. In fact, the package `pg` which connect node.js application to postgre SQL use this to achieve its goal.

They weren't used in javascript. Node.js introduced the concept of Streams and later js also introduced it using Streams API. From meta view, it says every thing in web is transferred using Streams. all the webpage, images or whatever. Even single images also.
### Networking
We will learn about Net, path, process, TTY, Read line, DNS and Dgram.

20 years ago, there was no networking to connect to two computer. There also was no Network Card in Computer.

However just after that switch was made. It is the second most basic thing in networking. First one is Ethernet cable. It just moves data in bits (ones and zeroes). So, we would have to buy a switch and connect it with our computer with ethernet cable. We have to configure how data is flowing in the switch. And our computer need to be able to accept the ethernet cable. To achieve that Networking  Card was introduced. At first, we would have to buy the network card but nowadays they come prebuilt. That Card has unique address associated with it. It is called ==MAC Address==. Each MAC address have 12 hexadecimal bits. All the PC and laptop companies are working together so make sure that MAC address in unique in all computer ![[Pasted image 20250106090731.png]]
Let's say we would want to send some data to Bob(we may be working on some company). So we would call that piece of data as ==packets==. We need to specify source, destination and data. It first goes to switch and it does the routing. So, the path is made for data to flow from one place to another. It is called ==network==

Below is the picture of real life switch. Each inputs(lets say 7) corresponds to one mac address. If data arrives saying send to that mac address, switch does the routing.
![[Pasted image 20250106091056.png]]

Now, what if we want to connect to another such network in another city. We can't do it like we did above. It will turn messy really really fast as there will be thousands of cable. 

In that case, we use Router on top of switch. It has certain IP address. Each computer, Each device has a unique IP address assigned to it. Router still understand MAC address also. The job of Router is to assign the IP address to all the computer that are connected to it. It also route data. IP address is like Home address. Now we can send data from one computer to another despite not being in same switch. And Router are also connected with cables. This is Internet. And remember each one has network card. And they can connect to the network using Ethernet Cable, Wi-Fi or even cellular connection. Now if we know src and destination IP address and port, we can easily send information from one computer to another in whole world. As every computer as many port, it works independently. let's say on port 3000, it might be sending website to another computer and on port 6000, it might be sending an image from another computer. Let's say one computer specifies the IP address and port number and password of another computer in network(given that another computer will allow that permission in network settings.). it will get whole access to that specified computer. We can even get access to whole disk . ![[Pasted image 20250106093739.png]]The job of ISP is to make sure that our device is connected to that vast network of internet. Even Sim Card already have those services that allows us to connect to that vast n/w.

When we are buying router, we aren't only buying router. In fact, we are buying router, switch and a wireless thing in it.

==Internet is the biggest network in the world. In fact when we are connecting to Wi-Fi which connects the laptop and mobile, we are connecting to private network==.

Networking Layers.
![[Pasted image 20250106110119.png]]

Physical Layers deals with cables. Meaning that information are transported using zeroes and ones. It has no idea about MAC, IP and data. just gets some zeroes and ones and it moves them across.

And comes Data Link Layer. This part contains switch. this part moves data and frames. They receive 0's and 1's and combine them in frame. So, they extract MAC address, the data, the destination MAC address and create the thing called frame. Then it will send the frame to destination MAC address(machine) using physical layer. So that machine will receive the 0's and 1's. And network card on that machine will transfer those into frame again.

Network Layer - Packets. It will use Router. Because Switches have limitation. As we need to connect to vast computer across globes, we need router. It uses packets and IP address. Network layer in itself is useless. Ya, you could get packets and could get some 0's and 1's and know where to route in network. Like it know which machine sent that and which machines to go. Let's say we want to send from Kathmandu to Delhi. This layer know which route to choose. And further it will choose another router on the basis of IP address. 

Transport Layer - Segments. It make sure that data are sent safely. If one of the packets are lost(which is pretty common), it tells source to resend that packet. It is about moving data and connection. It makes sure exact bits are send.

Application Layer. Now we know how to send data safely, we can now make sense of that data. In this Layer Node.js in implemented. Here we set up our HTTP server, FTP server or SSH or many more.

![[Pasted image 20250106110448.png]]
We deal with Application Layer. We don't need to know about other Layers. And also we are concerned with choosing TCP/UDP.

==SSH, FTH are built on top of TCP==.

==The main purpose of node.js is to develop networking application==. We already know how to move data. We need to create something value able with it.

`Net` module is the lowest networking module available in node.js. Every other networking module like `http`, `ssh`, `dns` is built on top of that `Net` module. `Net` module is just there to help us with whole TCP thing.

==Each networking application should have port number specified otherwise the request have no idea where to get routed to.==

Every time we create TCP server, we get IP address, family of that sever (whether it is IP4 or IP6) and PORT. If other specify the TCP request from anywhere in the world in that port with IP address, we can get the data(they will be able to send the information).

Understanding Network layer and Transport Layer.
It works with IP address. I get the message and breaks down in different packets and move it from point A to point B. These point could be anywhere in the world. But there is some approach. What if packets gets repeated ? or What if packet get lost(which is very common) ? What if point B isn't working ? What if there is data corruption. There is no way to figure it out. That is the purpose of fourth layer. It has no idea how data is transmitted. It just make sure data is received. Also, it specifies the port to transfer to specific application. Not to be confused with the naming convention, transport layer is not solely responsible for transportation of data. It is done in physical, data link and other layer also. The name transport layer is there because it will add some port numbers to the packets. Now, transport layer will identify the application layer. We know which application will need that data.
For transport layer, we have two main protocols to move data around. They are TCP and UDP. The goal of TCP is to move data from one point to another correctly. It does things behind the scenes to make sure that data is sent correctly. UDP doesn't care about that. There is no way for UDP to figure out that the packets are sent correctly. UDP is used in video streaming, Calls.
UDP speed >> TCP speed.
HTTP, SSH are built on top of TCP. In node.js `net` module is for TCP.

![[Pasted image 20250107200123.png]]
it works in three way handsake. First A sends packet to B and B acknowledge A that it received the packets and tells A to send more packets. There are some headers in place in TCP uses to make sure that all the data is received correctly.

![[Pasted image 20250107203251.png]]
It has sequence number. It shows packets number like 1,2 and 3 and 4. If the packet 3 is missing, Point B make sure it knows that missing information. Acknowledgement Number make sure acknowledgement is received that packet has reached to Point B it also holds information about the package that receiver is expecting. WE have also other headers also like window header. It indicates how big the packets should be. First packets are getting sent in very small chunks. and then it goes bigger and bigger. Infact, we see it in our download speed. also finished header. Which make sure packets is finished. There is also Data section to hold the data. And data is Optional. When we try to handshake, we don't send the headers. The whole thing is called Segment.
Segment = Headers + Data. 
Notice there is not Source, Destination IP address or MAC address. The reason is such that each layer add its own headers to the frame. Like Data Link add its own header to frame, so does network layer. TCP will add those headers to frame and give to Application layer. If it was UDP, it will add its own header. Network layer add Source, destination IP address and some. and so does Data Link Layer. So, We get the data from Application Layer and Transport layer add headers to frame and so does other layers and Physical layer send that data to other Point.

UDP Headers.
The size of UDP header is fixed at 64 bits or eight bytes. for TCP, it isn't fixed.
![[Pasted image 20250107204903.png]]

A Network scenario in action.
Let's say we have a computer and router in our Home and it may or may not be connected to the internet. That router comes with switch and wireless Card. But it will not work in Company like amazon or apple. They have their own router and switch configured for their personal use. Our router is capable for handling the cases for our Home. We may be using wireless(zeroes and one are travelling through waves) or LAN. 

Whoever designed this router have put the IP address to that router. That router gives IP address to that computer and tablet or new device that are connected to it . It is the job of router.

![[Pasted image 20250108132935.png]]
Let's say if Tablet put IP address of laptop and port number also, it will get the data if laptop provides such facilities. Port number writing is compulsory. Infact, a server is the device which sends certain information when IP address and port number is specified in the network. That server could send image or HTTP response or any other thing required like taking control of entire computer. 

When we make some server, it just means our machine(laptop) is sending some amount of information on the described PORT and its IP address(in our case it is loopback address). 
==Computer only understand IP address and nothing else==.

When we turn on persona hotspot in our mobile., we turn that device into router and switch. Now let's say we have made our phone a router. and we connect our laptop and tablet to that network. Then our tablet can talk with laptop. We just need to run our node app in that IP address(not an public IP address) of our laptop. If you use loopback IP address, then it will not work. Why ? cuz using loopback address, we can only send request from our machine only. Not from outside.
But we can get way from that restriction. Let's say we build another node app in another interface which can accept the request from public and redirect those that interface using loopback IP address, then we can respond right ? it is called reverse proxy and it is used by nginx server also. So nginx accept the traffic from public interface and redirect that traffic to the port number that we specify.

PORT Number.
It exist in transport Layer. You have specify it for communication. System port (0-1023). Don't try to use node.js in system port. When we visit `iana.org` to get information about PORT number, we can see some repeated function for same port. Right ? ![[Pasted image 20250108143059.png]]
So, technically you can create two application on a single port. One is for TCP and other is for UDP. But you can't have same port for same transfer protocol.
PORT 22 is for SSH port. This is a port used to make you connect to your server. If you buy a server or rent one from AWS or Digital Ocean and try to connect to it, you will use SSH. And if you occupy that port using node application, you're going to run into some issues.

==HTTP and HTTP-2 is built on TCP while HTTP-3 is built on UDP==.

When going to net module docs, we can see TCP transfer protocol and IPC servers. Infact, net module just concerned with communication. And IPC just helps in communication between two process. Like node.js have one process, database have another process. We need to connect those process at Operating System. So, IPC helps in communication between processes. And IPC isn't related to node either. It is facility of Operating System.
TCP when different machines needs to talk to each other and IPC servers when two process need in same computer need to communicate with each other. IPC server don't have any port number and IP address. TCP have it. If we see IP socket in node docs, then it is TCP. And Unix domain socket and piping is related to Inter-process communication. They are just way for talking with different process in OS.

Chat App
When we have two networking endpoint that are talking with each other, they are called socket in net module.

==Listen for socket in node.js ==
