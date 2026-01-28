In next.js dev mode, we may see `console.log` in browser console. It is to be noted that in dev mode, next.js prints console from server to browser. The code is running from server but just for DX, next.js does it. 

### 2️⃣ But the browser **calls these endpoints**

When you do:

- `useSession()`
    
- `signIn()`
    
- `signOut()`
    

The browser makes requests like:

`GET /api/auth/session POST /api/auth/callback/credentials POST /api/auth/_log`

Those requests hit the server.

