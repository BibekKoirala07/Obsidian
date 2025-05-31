 ### [[Best Practices]]
### [[Node]]
### [[ORMs & Mongoose]]
### [[Socket.io]]
### Beginning
![[Pasted image 20250102173919.png]]

### Event emitters in node.js
### Buffers
Buffers are the way to deal with zeroes and ones in node.js. To connect our technology which is node.js to connect to outside worlds.
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

The `res` object in node is writable stream. And for more look at docs.

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
## Networking
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

When going to net module docs, we can see TCP transfer protocol and IPC servers. Infact, net module just concerned with communication. And IPC just helps in communication between two process in same computer. Like node.js have one process, database have another process. We need to connect those process at Operating System. So, IPC helps in communication between processes. And IPC isn't related to node either. It is facility of Operating System.
TCP when different machines needs to talk to each other and IPC servers when two process need in same computer need to communicate with each other. IPC server don't have any port number and IP address. TCP have it. If we see IP socket in node docs, then it is TCP. And Unix domain socket and piping is related to Inter-process communication. They are just way for talking with different process in OS.

when we create client randomly in node.js, it takes the Dynamic PORT which ranges from about 40000 to 65000. you can check this by creating the 

Chat App
When we have two networking endpoint that are talking with each other, they are called socket in net module.
Remember TCP connection can happens from computer to computer or between computers in different country also. However IPC happens in same computer only.

==Listen for socket in node.js ==

### IP and IP address
IP(Internet Protocol) means network layer(how to get that packets from one endpoint to another). IP and IP address are completely different. Each computer in internet has unique IP address.
![[Pasted image 20250126180512.png]]
It is IPV4.
![[Pasted image 20250126181407.png]]
Actually IP Address has two parts. One is network portion and other in host portion. Subnet Mask also comes with IP Address. It indicates which portion is for network portion and which for host portion.
![[Pasted image 20250126181901.png]]
![[Pasted image 20250126182151.png]]
Router only understand Network Address. They simply don't understand Host Address at all. They only use Network address to traffic from one address to another. Host is always set to zero.

Internet consists of many network. Each of them have one router. And these router route the traffic from one network to another. Each network has its own network portion. The network (above one) which has 3 portion to network and 1 portion to host means that for that network there are 11111111(255) hosts. Let's say we have 62.37.7 network address for one router. that means it will support 255 devices. There is limitation. And one important thing is that, 255 is assigned for broadcast address. It means if we sent the request to 62.37.7.255(all ones for host), then it means it will send information to all the devices(host) in 62.37.7 network. Also for all zero means network as a whole. So we have to omit all zeroes and all ones. So we can have 254 devices in this case. what if host were just 3 zeroes. it how will omit 000 and 111 and will only have (7 - 1) host. just 6 devices.

One more thing to keep in mind is that there are private and public IP address also.![[Pasted image 20250126191415.png]]
If we use router, our IP address lies in between these. We can't connect Internet to these private IP address. All ISP block the private IP address if the request is coming from outside. And we can buy a router and assign the address to these IP address as long as our network is in private. The moment we connect it to internet(we go public), Our IP address is converted to Public address. The whole reason we have Private IP address is that we have low space in those 4 billion IP address. Let's say we have 200 people in a company. Instead of assigning 200 IP address to each computer, what we are going to do it we are using private IP address. It saves 200 spaces in 4 billion(not that much but people still did it.) Instead when they connect to that router, we will assign them public IP address, thus saving 200 spaces. When we try to assign Public address to our devices. Then we go online. Then we will face conflict. Our ISP might say that there is conflict. This is the thing that we should care.

We also have Loopback IP address
![[Pasted image 20250126192835.png]]
We lost 16 million IP address just cuz one person used this IP address with that masking for Loopback Address.

Let's simulate Two cases. One is if the sender and receiver are at same network
![[Pasted image 20250126194023.png]]
Let's say we want to send one packet to another in same network. If the network portion is same, then router don't do that much. rather it will just try to find MAC Address for the devices connected to that IP and use switch to route that package to device in same network.

Another case is if we want to send packets to different network.
![[Pasted image 20250126194628.png]]
First that packet goes to router and it immediately knows that that IP address isn't in that network. So, it looks at routing table. All router have routing table which contains network portion and they know this network portion is that router. So packet goes from that router to another router(that first router doesn't care what host it, it is for second router to solve). That second router again look for IP address and it gets that the network is same and it looks for host from there. It does that by sending request to all the devices, figuring out all the IP address, figure out all the MAC address. And the router will say which devices has this IP address with this host portion. one will respond that my host portion is matching and then, router will ask what is your MAC address and that device will give that. And the router using switch will route that packet to that machine. Notice, your MAC address will not go outside the router.

So, How are private IP address route to Public IP address ? 
remember, we can have same Private IP address but not same Public IP address. So how are private IP address to public Ip address ? We need to do that if we want to connect to internet ? We do that using ==Nat==.It is used by ISP to do so. It stands for Network Address Translation. It translates private IP address to public Ip address.
![[Pasted image 20250126201555.png]]
Let's say I have private address and I want to connect to internet then they will run the NAT application on top of router and what they do is they generate Public address. It will end up with public address. ==Video IP4== remained

Every city normally has its own IP address.
### DNS
Domain Name server
You will always be connected to DNS as long as you are connected to Internet.
![[Pasted image 20250126203719.png]]
It performs DNS lookups. we can do it using `DNS` module. The port number that dns module uses is 53.

![[Pasted image 20250127113829.png]]
With IP V6, the only thing that changes is networking layer. The way we code doesn't change if we change the version of IP address. If we use IP V6, now TCP will be built on top of network layer(now IP V6), it will take care of itself.

![[Pasted image 20250127114403.png]]
Each IP V6 is made up of 16 bits. The things is we can remove all leading zeroes. Like in last portion, we can remove 00 from 006F and just write 6F. IP V6 is written in hexadecimal.

##### About file-uploader app
Remember we are just using plain TCP. We weren't using any encryption at all. We shouldn't run plain http at all. we should run encryption on top of HTTP. It is called TLS. When we run TLS on top of HTTP, it becomes HTTPS.

### UDP Protocols
It is in fourth layer. Honestly the protocol like HTTP, FTP and other protocol are built on top of TCP. As a backend developer. we would rarely be working with UDP. Anytime information is important, we work with TCP. However HTT3 is built on top of UDP. There is one more layer called quick which is built on top of UDP, and that handles all these things related to data reliability. We use UDP if speed if our main concern.

UDP as opposed to TCP is connectionless. It means that for a specific endpoint for a specific socket. it doesn't matter whether that socket has endpoint or not. That socket can stand there by itself. In TCP, if we don't fire up server. client can't be connected. But not with UDP. it sends the packets and that is pretty much that. You can send the package and that is pretty much it. There is no guarantee that it is going to be received and also there is no acknowledgement also. And there is no way to get that notice whether the packet is lost or not.

Remember we have TCP and UDP in fourth layer. that means whatever is used in network and below layer are going to be same for both TCP and UDP. Only the way we transmit data is going to be different.

In UDP, there isn't going to be distinct sender and receiver at all. There are two version of UDP6(uses IPV6) and UDP4.
#### File upload
The project that I did is exactly how HTTP version one work. 

## HTTPS
![[Pasted image 20250126212859.png]]
It is built on top of TCP/UDP layer. HTTP don't establish the connection between two layer. That is the job of TCP/UDP and network layer and below layer. All the Http does is it governs the data that is being transferred using TCP and UDP layer. We just need to specify the data in HTTP. So,  

Each Http request consist of four things. One is methods, URL, headers and body.

The things is we can't access just `req.body` in node at all. Why cuz if we were able to do that then buffers will fill the memory, that will cuz huge pain in the ass. Rather than that node.js gives req as a stream. so `req.body` is a stream and when users sends gigabytes of data in `req.body` and it will handle without creating memory issues as we accept data in chunks.

How are HTTP connection done ? 
![[Pasted image 20250126223529.png]]
First we we establish TCP connection between client and server. Then we are going to send first TCP segment. That first TCP segment contains method and URL. After it has passed, client sends the body if there is one in second TCP segment. It sends in chunks in number of segments if required. However at last chunk, it should send the indicator that request has ended. It could be /n or /t or some few bits message. That whole TCP segment is called message. Now the turn is for Server to send the message. It also send the message just like it 
![[Pasted image 20250126224741.png]]
For response Server send Code Text at first which indicates whether the request has been successful or not. also header is sent. They both are send in one TCP Segment. and body are send in chunk(stream). That is the reason why it doesn't hangs when we send huge data in body. Just like request, last chunk need to have \t or \n to indicate end of body

What exactly happens when TCP connection is done ?
We have two options. WE can kill it and we can keep it. In HTTP1, there was no option. But in HTTP 3 there is. we can keep TCP connection alive to make sure that we can use same TCP connection to make sure we use less energy.

###### Understanding Connection Type
We can do two things after req/res is done. We can reuse that TCP connection and also terminate it. We have one option is that we can keep req/res in caching. This is important concept in HTTP. Let's say client keep sending request for specific CSS files. The best browser can do it to cache the body chunks as CSS file. Later if client wants to  send another request to fetch the exact same CSS file, then client don't need to establish the TCP connection again. 

We can keep TCP connection alive by header `connection: keep-alive`.

Doesn't creating TCP connection just require few millisecond? 
Well, do simple google search. Go to network tab and see the amount of request. there are probably `100` requests. What if for each request we had to create TCP connection for every request if we need something ? Well may be it requires 20 millisecond and 100 request. calculate yourself. It requires some times. In this cases keeping `connection: keep-alive` is beneficial.

There is options for `keep-alive` in HTTP1 and there isn't that options for HTTP2 and 3 because the connection is persistent.

![[Pasted image 20250202182438.png]]
Here we also have option for keep-alive: timeout means if there was no second request for 8 second then kill that TCP connection and at one server, only 800 TCP connection can be established. So if we already have 800 connection and new client want to connect, we have to kill one one connection. There are algorithm for that. Another thing to care is that when client goes to another webpage. that TCP connection will be killed automatically. Or if we change the IP address (like changing from mobile data to WIFI, we have changed the IP address,  we are establishing new TCP connection). But for HTTP version two and three, connection are persistent by default. Those two headers are not accessible in HTTP version two or three. If we use, it will be ignored or will create error

###### Building our client and sending a request
Postman only support HTTP1. Node itself doesn't support HTTP3. But we can create proxy in node.js to support it. We normally create HTTP1 and use proxy to support HTTP2 and 3 to get best out of all world. The main thing to know is that node is just used to send and receive the message in TCP application. Let's say we put random request from postman using form Data or GraphQL. Node.js doesn't do parsing at all. We have to do that. Even when we do request to data

###### Learning Docs
WE have two things in Http. Either a server or client. 
![[Pasted image 20250204131243.png]]
We have two things which are shared between server and client. They are `http.IncomingMessage` and `http.OutgoingMessage`. In server we have `http.Server` and `http.ServerResponse` and latter one is inheriting from `http.OutgoingMessage`. 

We have two more thing that are same to both server and client. They are `http.IncomingMessage` or `http.OutgoingMessage`. Either they are coming to the endpoint or going out of the network.

==Cookies are just header==.

###### When does net becomes http
When we try to send send request from net module to http server, it doesn't work. It says wrong request format. There is a specific format for https request to be accept. We have to encode the headers like `content length` and other things to be in header and then it will read body. 
 We can emulate the http module with net module but sending header like in http module. We can do that by encoding that in Buffer as head and body and sending them to http server. It would work.

###### Emulate http using net module

###### understanding media types
we can specify `content-type: some media type` for request to know what does client need or webbrowser needs.
![[Pasted image 20250204151750.png]]
there are two different kinds of media type. ![[Pasted image 20250204151901.png]]
Discrete means only one file and multipart means multiple file Boundary in multipart is to differentiate between different files which is gotten from files. Without it, we may not be able to differentiate between files if we send multiple files in multipart class.
###### understanding http methods
By understanding Idempotent, we will have easier time understanding http methods. We call some function or operation idempotent if doing that operation multiple times will not have any additional effects on server except the very first one and result is same for every time.
![[Pasted image 20250204153409.png]]
If we delete File over One GB, we can just do it one times. However ,delete biggest files can be done any amount of times
![[Pasted image 20250204153736.png]]
![[Pasted image 20250204153857.png]]
Well, if we try to create multiple comment in our server. it should create multiple comment in our server. So, No Idempotent
![[Pasted image 20250204154007.png]]
![[Pasted image 20250204154241.png]]
![[Pasted image 20250204154423.png]]
![[Pasted image 20250204154602.png]]
It is normally used for caching and sometimes used to download large files also.
![[Pasted image 20250204155018.png]]
Options is used to know about things like CORS, headers and other things.
###### understanding http statusCode

###### creating our first web server
there is difference between web server, http server and net server and other things. Web server means serving the web. If our server is dealing with HTML, then it is called web server. If we create a Web server, anyone in the world can access it with just a browser. They don't need any technical knowledge about anything like when we made chat and file uploader app. in that app, the client needed to know the way to sending request. In web server, client don't need that at all.

The thing is if we provide html to browser and there we have specified css in html. Browser will try to fetch that also. it is going to access all the document or resources that is linked with that HTML. If the HTML has image, then look at host. if the host is of same server, the browser will request for that image from server. otherwise if the host is other website like unsplash then browser will automatically send HTTP request for those.

==T he main thing to know is that js as a language is small and browser and node.js add their own thing to js and make it work. Browser add document object to js and make it work in browser and node.js add modules in js and make it work+==
###### understanding http proxies
Lets say we have three node js server running same application but they could have different IP address or PORT. Image one of them goes down(which is pretty common). Load are distributed among these server rather than one server serving all requests

How can we distribute the traffic evenly ? One thing we can do is http proxy. In this case that proxy is load balancer(has its own IP address). It is not that difficult. All that proxy means is that it will grab the traffic from client and will route that to server or any other endpoint and get a response from that server and will give that to client.  There are many algorithm to set up which server to traffic to. We can also block some user that may be harmful by using proxy. It could also modify the header of request or response. Let's say we have another proxy between Load Balancer and client and it uses HTTP3. means all our client are using HTTP3. But our server may only support HTTP1. Still it will work.

About speed. All these proxies could be on same network. So we don't care about speed between proxy and client. What we care is speed between client and proxy. In some cases proxy could be HTTPS. but we don't need to take care about that in our node code. See how beneficial.
![[Pasted image 20250205125118.png]]
We can built proxy in node easily.

###### stateless
means each request is independent and not related to previous or next request TCP is statefull. However http isn't statefull. How to make ![[Pasted image 20250205130932.png]]
Let's say client 4 sends request to LB and then to Server 1 and just after that again client 4 sends request to LB and it route to Server 2. then Server 1 will not know if the client sent that request to server 2. also if Server 2 get the request from client 4 after some time even then Server 2 don't know that same client requested something sometime ago. The thing is Can we use TCP to make HTTP statefull. Yes we can. But it is not good at all. why ? TCP connection is made everytime client goes to website. he could change the wifi or revert to mobile data and new TCP connection will be made and session will be new. If client turn wifi off and again on, TCP connection is new. 

How to save that token ? We have to dwell in cookies.
###### cookies
Cookies is directly sent from web to server directly.
![[Pasted image 20250205132129.png]]
Lets say web is asking for response from server. We want to keep track of user. We need to provide cookie in header of response.
And the convention is that whenever the data is received from `set-cookie`, the data is being saved in client hard drive by default. It may be somewhere but it is there. That means client can have access to that data even if computer is turned off as they have saved it in hard drive. 

Now they are sent to server by default. 
![[Pasted image 20250205133018.png]]
And the convention is semicolon with space. Sometimes we don't want to send the cookie or might want to send cookie to particular path only. There is option for that. But by default, cookies are sent in every single request.

Ideally we don't want client to modify that cookie. Of course javascript does have power to do that. By WE don't want that.

Javascript can access cookie by default but there is option to restrict that. For all those options refers to docs

Cookies aren't the only way to store data in client's browser. we can use local Storage or indexed DB. 

==Normally tokens are saved in Database remember this thing==
### Unix
The whole Internet is running in top of Unix.
![[Pasted image 20250205173223.png]]
Unix was first written in assembly so, it was problematic as it had assembly code. For assembly, we have to write code for specific hardware. If we write code for specific CPU, we can't run on different CPU. So Unix had same problem. So what Dennis Ritchie did was he wrote C and kept improving C so he can write entirely Unix in C. That made it portable. So for the first time, we could run same OS in different CPU. C is heart of Unix.
![[Pasted image 20250205180050.png]]
The derivatives of Unix are BSD, Linux. Linux was made by C. The thing is Linux and Unix are vastly different. Linux is built entirely from scratch however takes it's principle from Unix(not copying).
Another one is MacOS and its kernel is called Darwin. Apple took BSD and kept improving it. MacOS adheres to Unix principle. So android is also Linux based which is Unix based(took inspiration only not copy)(just like if we built framework like express but we didn't copied that). iOS is also other one .

==When we say Unix, we are referring to an OS that adheres to Unix concepts==. Nowadays, when we say we are building application for Unix, It means we are going to build app for its derivatives. We aren't referring to original OS that was built around 1970s.

For Most cases, if we can do something in Unix, we can also do it in its derivatives(Mac OS) as they follow same principle. 

We specify the Operating System on the basis of how they:
- they manage users,
- the file permissions,
- the way that their file system is structured,
- system libraries,
- the way that they communicate between different process,
- environment variables,
- process management
- and many more
When we say Unix based that means they share some of the principle described above. Unix is more about principle and theories.

Unix philosophy is modular design(as simple as possible) we build different program that do things really well and set up a system between then so that they can work together. (IPC communication) as pipes. also shell scripting means that combine these tools to perform complex workflows. You can see Unix Wikipedia and find that it is more about communication between program to complete the task rather than program itself.


