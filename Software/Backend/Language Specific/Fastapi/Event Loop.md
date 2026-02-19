Python has Process Pool Executor.

Thread Pool handles Fastapi not Event Pool

Fast api has dual modes. async def -> event loop Like Node.js
def -> run in the thread pool like Java spring.

Python async also has extra rules: you must await I/O or else you block the loop. 

We can mix async + sync in one app - powerful but adds complexity. 

Fastapi end points mode. 
- FastAPI (via Uvicorn) runs **each sync request in a separate thread from a thread pool**.
- Each thread can block freely without stopping other requests.
- Thread pool size = limited (default often 10–20 threads), so if too many long requests come, requests will queue until a thread is free.
**use sync when:**
- Your code is CPU-heavy
- Or uses blocking libraries (like most SQL libraries or file I/O)

### **B. Asynchronous endpoints (`async def`)**

```python
from fastapi import FastAPI 
import asyncio  app = FastAPI()  

@app.get("/async") 
async def async_endpoint():     
	await asyncio.sleep(1)  # non-blocking wait     return {"message": "Hello from async"}
```
**How it works:**
- FastAPI runs the endpoint **inside the asyncio event loop**.
- While one request is waiting (like `await asyncio.sleep(1)` or a database call using `async` driver), **other requests keep running**
- No extra threads are needed for I/O waits.
    

**Use async when:**

- Your code is I/O-bound (HTTP calls, async DB queries, reading files with async libs)
    
- You want **high concurrency without using many threads**


