It is pain in the ass
## Render
If there are issues with rendering, try not using the products that are in beta and move `vite` , `@vite/` to dependencies and not dev dependencies
#### 
![[Pasted image 20241025133307.png]]
I have done the following and it has worked.
1.i run `npm update;`
2.`npm install autoprefixer;`
3.`npm run dev;`
I did those 3 steps and the error was solved

> [!NOTE] Put all in dependencies rather than dev dependencies
> It suddenly started working when I put everything in dependencies
