How python runs ?
By default Python runs like this 
```python
One Process
One Thread
One Call Stack
Line-by-line execution
```

If we do 
```
import time

time.sleep(5)
print("Done")
```

Basic python = blocking + synchronous

Normal python server is synchronous and is blocking. So python needs concurrency. 

Python supports threads with GIL.
```
import threading
```

GIL = Global Interpreter Lock

Only one thread executes python code at a time. 

```
Thread1: running
Thread2: waiting
Thread3: waiting
```
No true parallel CPU work (in Python)
threads helps with I/O, not CPU at all. 

And guess what Python also has
```
async + await
```