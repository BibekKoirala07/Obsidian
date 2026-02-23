### Persistence
Supabase provides us with translator which changes the sql to supabase client code. 

every non aggregate select target must exists in group by clause if aggregate function is used. 
### Authentication
when signed in supabase issues the token and put it in local Storage. The client library automatically takes that data from token(jwt) and creates a session out of it.
the things that use is logged in or not is proved by jwt that is stored in browser and not session or thing like that. a stateless auth system.

onAuthStateChange is used in client side. 
```ts
supabase.auth.onAuthStateChange((_event, session) => {
setSession(session);
console.log('Session changed:', session);

})
```

anon is jwt as it is first thing that says yes that user is correct

that anon key means anoymous. and that anon means supa base client makes request as a anonymous person and that payload in anon token contains roles. They are anon and authenticated. Like this is public request or this is full blown authenticated request

anon key 

jwt authenticated. 

PostgQuery builder. 
### Storage
It may seems like storage is part of postgresql but it isn't. We have things like 
- Buckets
- Signed URLs
- ACL(private/public)
And they aren't the part of PG at all. it is a separate API. 