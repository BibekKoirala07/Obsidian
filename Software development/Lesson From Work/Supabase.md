### Persistence
Supabase provides us with translator which chnages the sql to supabase client code. 
Aggregate function are disabled by default in supabase due to performance risk. We can enable it in supabase sql editor
every non aggregate select target must exists in group by clause if aggregate function is used. 

realtime subscription/events
![[Pasted image 20251218112225.png]]
turn the publications on in database publications and also. 

![[Pasted image 20251218112707.png]]

### Authentication
when signed in supabase issues the token and put it in local Storage. The client library automatically takes that data from token(jwt) and creates a session out of it.
![[Pasted image 20251218115409.png]]
the things that use is logged in or not is proved by jwt that is stored in browser and not session or thing like that. a stateless auth system.

onAuthStateChange is used in client side. 
```ts
supabase.auth.onAuthStateChange((_event, session) => {
setSession(session);
console.log('Session changed:', session);

})
```

anon is jwt as it is first thing that says yes that user is correct

that anon key means anoymous. and that anon means supabase client makes request as a anonymous person and that payload in anon token contains roles. They are anon and authenticated. Like this is public request or this is full blown authenticated request

anon key 

jwt authenticated. 

PostgQuery builder. 