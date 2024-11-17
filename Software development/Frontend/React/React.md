Todo: 
- [ ] Learn about how react works under the hood and  write about it so I don't need to learn multiple times

> [!NOTE] Here is the link about React
> ([React posts (dmitripavlutin.com)](https://dmitripavlutin.com/tag/react/))
### Here are some tips about react
1. **Always use npm package**. Don't try to be smart-ass. If my future self is watching this. remember that me at the time of writing this tried to do all the logic by myself like carousel, date formatter. I get frustrated and wasted my time. So, don't write repetitive logic and use package whenever possible.
2. **Always use Custom Hooks**. It may seems to be hard. But is is way too clean not to use. The goal is to separate component rendering and logic as much as possible. You can use it in data-fetching.
3. There is no need to know react state management libraries at all. They change from time to time and new one come claiming to solve all problem. We learn it at the time of making projects. At the time some of them are `redux`, `zustand`, etc. 
4. **Always use ==`Typescript**`== as it makes things easier. It may be harder at first but it will pay off.
5. **Use tailwindcss**. Later in project the CSS conflict will be some much that You will have to abandon the project. It is me telling now. So, don't repeat the same mistake.
6. **Use packages like react-query**: try to use data fetching library but it isn't compulsory. because if we don't use it then we have to implement extra states like `loading`, `error` and other states to know the condition of fetch request which is tiresome many times. unless done by some AI. so try to use packages like react-query
### When to use package ?
Everywhere. Remember don't be smart ass and try to make CSS from zero. it is redundant. Everytime you need carousel, toast, need for our project to support multiple language, charts, graphs, etc. you should use `npm` package. Everytime we create from scratch, it is a pain in the ass. I am writing this from experience for the past.
## ==Hooks :== 
### Tips 
1. There is not need to know about every-hooks available at all. Just know majority like useRef, useState etc.
### useState
1. If you forget that the state variable updates on the next rendering, you might try to read the state variable right after changing it. Unfortunately, it won't work. Suppose we update date of birth at some point inside some function. Don't update age right after it. remember it happens asynchronously so age will be calculated using the previous Date of birth value. ==don't use state value to change value if possible right after it changes== 
```
setState(newValue)
console.log(state) // the value of state is still oldValue
```
### useEffect

> [!important] To know more about it
> [ Open Link to know more about UseEffect ](./useEffect.mhtml)
 
> [!NOTE] Here is another one
> [Link to know]( ./useEffect-overreacted.mhtml )
### useCallback
Sometimes we send functions as props. React treats that function as new prop everytime component re-renders even if function itself hasn't changed. `useCallback` is used to avoid rendering again.
Tips: 
1. Don't overuse it
### useMemo
Sometimes when Parent Re-renders but children have same props. then it is no need to render that child component, right ? Exactly. Why does Child Re-renders ? Cuz props is an object and in js no object are same remember ? But we don't need to render that child component. So `useMemo` is used. Another use is we can memoize values also. Like we want to calculate the value of derived variable only if the variable that change the derive variable change. It can be confusing but main use it to memoize component and any value.
Tips: 
1. Don't overuse it.
### useRef

> [!Note] What is useRef
> The gist of useRef is that it doesn't re-render the component. We may need useRef to access DOM elements also. For more info search in internet
### useTransition

> [!important] To know more about it
> [ Open Link to know more about UseTransition ](./useTransition.mhtml)
###### Redux toolkit
Tips: 
1. Use `redux-persist`. if you don't use it will cause rendering issues. Like, we will have hard time making automatically redirection. So don't try to implement it using manually. Don't be jerk at all.

## ==npm packages==
#### Redux-Toolkit
###### rtk-query

