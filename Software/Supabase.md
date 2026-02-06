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
The main thing in SB about  key is Publishable key
### Auth
In forgot password form, when wrong email is sent, then also SB consider it as a success. If we gave away the info like user doesn't exists, then hacker can get info about user's email using Ddos attack. 

When We do reset password and it becomes successfull, SB create a short session for them. The user don't need to login after a successfull reset password. This is the default feature.

In Whole of SB we have two states in terms of authentication. Like One is `aal1` and another is `aal2`. We need to understand it deeply to understand auth. When we log in, there is the field in auth session where we can check the value. We can see it in `claims`

`aal1` just means user is email and password authenticated. and `aal2` means the user is email and password authenticated and also MFA authenticated. Now imagine, if the user has disabled MFA. Then `aal1` is sufficient to know that user is authenticated. But what is user has MFA enabled but has `aal1` ? then it means in current state, user is email and password authenticated but isn't MFA verified. right ? We clearly need to check this thing very thoroughly.

So technically
```js
if (hasMfaEnabled && aal != 'aal1') {
	block // it means that user has MFA enabled but the user is only email and password authenticated. not MFA verified. 
}
```

1Password, Authy, Google Authenticator or Apple's Keychain

In SB, MFA isn't compulsory. User can activate it based on themselves. There is no button to enable MFA for all users. 
### Authentication with Service Providers. 
Account linking is OFF by default. 

As long as the email account is verified with verification email, SB assumes that that email provider and google provider or other providers are same. 
Now, as long as the Google email is **verified**, Supabase assumes the person owning the Google account is the same person who created the email/password account and merges them automatically to prevent you from having duplicate users for the same person.
This was done to create the friction less experience. 

Same will happen if we do it with adding facebook also. 

If we want to stop this we have to shift to manual strategy. Supabase always assume that you want a friction less experience. 
###### Important Security Note
If you ever decide to allow users to **change their email**, keep in mind that Supabase will handle the update across the user record, but the "Provider ID" for Google will stay the same.
### RPC
### Web Hooks
### Edge functions
It may sound fancy but edge function is small, stateless(no memory between request) server-side function that runs close to the user on demand. LIke in previous times, if the server is in New York and the app is running in Nepal, the app had to send the request all the way to new york. Well that is latency. 

But instead of doing all that. Imagine the user want data it can go to nearest `edge` from Nepal. In Supabase it is distributed across hundreds of global data centers(via the cloudflare or SB network).

Why are they preferred ? They scales automatically. We don't have to manage servers, scaling, load balancing and OS patches. Like backend endpoints without owning a backend. Just write `index.ts` and put there that is it. 

Unlike old school "lambda" funciton, the edge function start instantly. It used to lake 2 second for lambda function to start. For SB EF it takes about some milliseconds. 

And simple, Secrets and complex logic aren't visible to user's browsers. 

==Edge function in SB uses `deno` runtime== 

It shouldn't be misused that much. Only when we need to hide logic or are using to communicate with third-party API, communicating Web hooks, and doing heavy lifting and do things like image and data processing, we should use them. We can also use it if external API calls are also involved. 

Now there is double edge sword with latency. Imagine EF is in Mumbai and database is in New York. No matter what you do, the function has to talk to DB all the way across ocean. So you gained nothing by doing this. 

And Cold Starts. They are small but real. Direct DB query via SDK are faster for simple tasks. 

### Supabase/ssr
We know SB is session based i.e. access Token and refresh Token.

Basically SB/ssr is a helper library that makes auth work correctly in server component by managing cookies across server and client boundaries. 
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

### Best Practices in Supabase
For authentication, don't try to make yourself, supabase already gives you template. Don't try to be too much. 
== There is a section for Security and Performance in supabase in Project Overview Section. It uses `HaveIBeenPawned.org`. You should enable this. 


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
