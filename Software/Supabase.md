Supabase = PostgreSQL + Edge function + Real time + Storage  + Auth

### Auth
### RPC
### Edge functions
It may sound fancy but edge function is small, stateless(no memory between request) server-side function that runs close to the user on demand. 

Why are they preferred ? They scales automatically. We don't have to manage servers, scaling, load balancing and OS patches. Like backend endpoints without owning a backend. 

==Edge function in SB uses `deno` runtime== It requires very less amount to cold start a EF in SB.

### Pricing



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