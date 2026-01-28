Supabase = PostgreSQL + Edge function + Real time + Storage  + Auth

### PostgresREST
It is a server which converts each table in supabase to REST API automatically.

Suppose we have `users ` table then that table becomes a endpoint. For eg. `/GET users`. We actually can get row by doing this. and also we can get further more by this `/GET users?select=*,posts(*)`

Supabase client converts our code into REST calls
for eg.
```
supabase.from("posts").select("*")
```
becomes 
```
GET /posts?select=*
```

### Supabase Keys
### Auth
### RPC
### Edge functions
It may sound fancy but edge function is small, stateless(no memory between request) server-side function that runs close to the user on demand. 

Why are they preferred ? They scales automatically. We don't have to manage servers, scaling, load balancing and OS patches. Like backend endpoints without owning a backend. 

==Edge function in SB uses `deno` runtime== It requires very less amount to cold start a EF in SB.

### Supabase/ssr
We know supabase is session based i.e. access Token and refresh Token.

Basically supabase/ssr is a helper library that makes auth work correctly in server component by managing cookies across server and client boundaries. 
```
```
==It help us create supabase client that understand SSR==. This clients know where do cookies comes from , know how to set cookies and works in both client and server environment.

Basically they handle cookies for you. 

==We don't need it in pure SPA==.

The biggest need is that Server component don't own the response. We can off course get the value from cookies in server components ==as they can see the request==. But since they don't own the response, we can't write it at all. So who can write it ? Well only route handler can write it. 

So let's look at one possibility ok. One fact is the ==supabase refreshes token automatically==. And this is quite a problem actually. 
So let's say In server component we do 
```js
supabase.auth.getUser()
```
Now what if access token expired at that time ? Well Supabase refresh automatically right ? but What if that updating happens in server component. Well Server components can't write the cookies in browser as they don't own the response
```js
cookies().set("", '') // This is illegal in server component as SC dont' own header, response and don't have set function at all. Even if it compiles in server component, it willn't behave at all. 
```
So what will happens ? Well new token must be received to cookies but guess what ? It never happens at all. Browser never receives updated token. Now the next request becomes problem. 

This library exactly solves this problem. So this library makes extra route handler and performs token refresh, sync session and writing updated token in route handlers and send it
```js
export async function GET(req: Request) {
  const res = new Response()
  res.headers.set('Set-Cookie', ...)
  return res
}
```
It just routed the cookies mutation/handling to the place where it is legal(to route handler or middleware). One thing is that we can do cookies handling in middleware also. 

This is one of the problem that you will face if you don't use this library. 

But There are other problem also. 
Each SC is executed independently. Even if they are part of same page. Now What if we had this
```ts
Layout.tsx
Page.tsx
ChildComponent.tsx
```
They are executed independently 
and they all have this
```ts
const supabase = createServerClient() // cause SC dont' share session so we need to do this for multiple SC.
const { data: { user } } = await supabase.auth.getUser()
```
What if accessToken is refreshed for all the component at same time ? 3 api calls yet that token will n't be written in browser.

So we need `supabase/ssr` package to handle everything. 


### Pricing

### Things to know
Supabase client already provides us the facility to turn SQL to supabase client code.
Aggregate function are disabled by default in supabase due to performance risk. We can enable it in supabase sql editor.



## What do people actually use Supabase Edge Functions for?

This matters more than theory.

### 🔥 Common real-world use cases

#### 1. Secure logic (VERY common)

Frontend should NOT:

- Hold secrets
    
- Talk to Stripe
    
- Call internal APIs
    

Edge function does:

`// Stripe webhook, payment verification, etc.`

---

#### 2. Bypassing RLS safely

Frontend is restricted by RLS.

Edge function:

- Uses **service role key**
    
- Can bypass RLS safely
    
- Still enforce logic manually
    

Used for:

- Admin actions
    
- Batch jobs
    
- Internal APIs
    

---

#### 3. Webhooks

Supabase Edge Functions shine here:

- Stripe
    
- GitHub
    
- Payment providers
    
- Email services
    

---

#### 4. Data orchestration

Example:

- Create user
    
- Create profile
    
- Send email
    
- Log analytics
    

All in one atomic flow.

---

## 7️⃣ What edge functions are NOT good at

Here’s where people screw up.

❌ Long-running tasks  
❌ CPU-heavy work  
❌ Stateful workflows  
❌ Cron-heavy jobs  
❌ Background queues (without external help)

They are **request/response**, not workers.

---

## 8️⃣ Big misconception: “Edge = faster database”

No.

Even if your function runs at the edge:

- Your **database lives in one region**
    
- DB latency still exists
    

Edge functions help when:

- Logic is near the user
    
- External API calls are involved
    
- Auth checks happen before DB
    

---

## 9️⃣ Mental model that will keep you sane

Think like this:

> **Frontend → Edge Function → Database**

NOT:

> Frontend → Database (for everything)

Use edge functions when:

- Secrets are involved
    
- Logic must be trusted
    
- RLS becomes painful
    
- You’d normally create a backend endpoint
    

---

## 10️⃣ Straight talk for a backend dev (you)

As a NestJS + backend-minded person:

- Supabase Edge Functions = **mini controllers**
    
- No services layer
    
- No DI container
    
- No state
    
- No long lifetimes
    

They’re **surgical tools**, not an application framework.

---

## Final takeaway (remember this)

**Edge functions are not special.**  
**They are just server-side code with great ergonomics.**

Supabase didn’t invent them — they just:

- Integrated them tightly with Postgres
    
- Removed infra pain
    
- Made backend optional
    

If you want, next we can:

- Design a **real flow using RLS vs Edge Functions**
    
- Compare **RPC vs Edge vs Frontend queries**
    
- Map Edge Functions to **NestJS controllers**
    

Just say the direction.