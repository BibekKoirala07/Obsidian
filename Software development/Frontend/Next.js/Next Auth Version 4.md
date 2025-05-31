###### next-auth
Setup api route which is in 
1. `app/api/auth/[...nextauth]`
```js
import NextAuth from "next-auth";
const handler = NextAuth({}); // it takes some options. but we need those configuration in multiples places
export { handler as GET, handler as POST };
```
We are going to create `auth.ts` in config folder somewhere. As NextAuth takes somes configuration options, we are going to define it in different files. and reuse that later
2. We are going to use OAuth here and  that is it. The goal is to get the code(id) from providers sections. Go to `Google Console` -> `Api and Services` -> `Credetianls` -> `create credentaisl` -> `OAuth Client Id` -> `Configure Consent Screen`(the screen that user will see) -> `External` -> Fill everything and -> and when you choose the scope![[Pasted image 20250418230212.png]]
and this is minimum that is required. that `email `and `openid` field is minimum. If you are using google services and using the user info and accessing the access token on their behalf, then you need to do more.  -> create test users also -> Create a client id, choose web application and put callback url, that call back URl is provided in next-auth providers sections. -> Copy the client Id and Client secret. That is it
```js
import { NextAuthOptions } from "next-auth";
import GoogleProvider from "next-auth/providers/google";
const options: NextAuthOptions = {
  providers: [
    GoogleProvider({
      clientId: process.env.GOOGLE_CLIENT_ID || "",
      clientSecret: process.env.GOOGLE_CLIENT_SECRET || "",
    }),
  ],
};

export default options
```
3. Now copy that options to `route.ts` that we made
```js
	const handler = NextAuth(options)
```
By default Next-auth doesn't store the session in memory and store the session in jwt.

4. By default, next auth provides the thing to sign in.
```js
	import signIn from 'next-auth/react'

	<Button onClick(() => signIn("google"))><Button> // pss int eh provider that we                  would want to login with
```
Now whether the user is logged in or not, next auth provides the hook to check. That is called `useSession`. but it needs to be in client component. 
Like 
```js
	const {data, status} = useSession();
```
and actually to use this hook, we need to wrap the app with `<SessionProvier></SessionProvier>`
4. We also need a `NextAuth URl` and `Next Auth SECRET`(to encrypt json web token) also for Secret there is command that you can run `open ssl` and something. Now after that we can also get the server session in server component using `getServerSession(options)` available in next-auth.
5. We can redirect the user after it is logged in. like from `signIn("google", { callbackUrl: "" })`. This option is also available in `signOut` also
6. Now what we need to do is to prevent user to go to protected route. Right now it is possible even when user is unauthenticated. We are going to use `middleware.ts`. It should always be sibling to `app` folder ok. 
```js
export { default } from "next-auth/middleware";
export const config = { matcher: ["/profile"] };
```
7. We can not not enter authenticated route. but we can also change the things also. Let's say unauthenticated user go to that `profile` route, it is permitted. but it shows default next auth display. We can change that with next-auth. We have facility to describe to next auth where they should go if there was error, for sign in and sign out and other things also.
```js
const options: NextAuthOptions = {
  providers: [
  pages: {
	  signIn: "/",
	  signOut: "/auth/signOut"
   }
    GoogleProvider({
      clientId: process.env.GOOGLE_CLIENT_ID || "",
      clientSecret: process.env.GOOGLE_CLIENT_SECRET || "",
    }),
  ],
};
```
