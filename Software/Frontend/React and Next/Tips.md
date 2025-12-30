### For overall architecture
We should always optimize the Images before deploying. Don't point font to google font rather just download them and use it. It is better. Use fewer and smaller images. Enable Gzip and brotli compression.
Anyhow reduce HTTP request that is done from your first load. Don't let anything block the render. Rather use `Supense`. Lazy load everything. Don't use plugins that much. You will suffer later. Rather make the component and put somewhere and use it. Don't use that much fancy animation. User don't want fancy animation to somehow make them belief of fake speed. They want no loading spinner.
### For development tips
1. **Always use npm package**. However there is a catch I will tell it at last. Don't try to be smart-ass. If my future self is watching this. remember that me at the time of writing this tried to do all the logic by myself like carousel, date formatter. I get frustrated and wasted my time. So, don't write repetitive logic and use package whenever possible. However every package adds the extra http request. So choose wisely
2. **Always use Custom Hooks**. It may seems to be hard. But is is way too clean not to use. The goal is to separate component rendering and logic as much as possible. You can use it in data-fetching.
3. **Always optimize the image**: Use whatever website or medium is necessary but seriously optimize it
4. There is no need to know react state management libraries at all. They change from time to time and new one come claiming to solve all problem. We learn it at the time of making projects. At the time some of them are `redux`, `zustand`, etc. However learn redux so that You can use it as a solution
5. **Always do tree shaking**: It is nothing but removing unused code in production. That is it.
6. **Always use ==`Typescript`== as it makes things easier. It may be harder at first but it will pay off.
7. **Use tailwindcss**. Later in project the CSS conflict will be some much that You will have to abandon the project. It is me telling now. So, don't repeat the same mistake.
8. **Use packages like react-query or rtk query**: try to use data fetching library but it isn't compulsory. because if we don't use it then we have to implement extra states like `loading`, `error` and other states to know the condition of fetch request which is tiresome many times. unless done by some AI. so try to use packages like react-query or rtk query.
9. **Try to use Framework**: it may be tempting to use just React, but don't do it, just use Next.js or Remix. Even React docs says so.
10. **Use the package which helps in internationalization**: Means which helps in different language.
11. **Use React Intersectional Observation and Lazy-Loading**: Use the package so that data in bottom of the page which isn't visible for now will not be fetched when we are at the top of the webpage. 
12. **Use react-ga4**: Use package for analytics.
13. **Use accessibility package**: Use package for accessibility, it will be pain in the ass later.
14. **Always use redux and rtk query in MERN stack**: Let's say we have some number of message that are unread or whatever that needs to be show in sidebar and what will you do after going to the component and let's say user change that message from Unread to read. what will you do ? not update that no of message in sidebar ? . rtk allows that to go outside that.
### When to use package ?
Everywhere. Remember don't be smart ass and try to make CSS from zero. it is redundant. Everytime you need carousel, toast, need for our project to support multiple language, charts, graphs, etc. you should use `npm` package. Everytime we create from scratch, it is a pain in the ass. I am writing this from experience for the past. But there is the catch. man, (make the component and put it somewhere).
###
