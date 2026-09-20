Process(application) a unit of execution, that has its own memory space

Each application has its own memory space, also known as the heap. 

the heap isn't shared at all between two application

A thread is a single unit of execution, within a process. Each process can have multiple threads. Main thread. Code run a main thread. 

Each thread created by a process, shares that process's memory space, the heap. This can cause big problem within our aplication

Thread stack. (a memory which only single thread has access to). 

Every process has a heap and every thread has a thread stack. 

offload run running tasks. A web server is another use case of many threads, allowing multiple connections and requests to be handled, simultaneously. 



