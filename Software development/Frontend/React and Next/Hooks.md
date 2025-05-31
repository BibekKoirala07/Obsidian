### ==Tips==

### use State
It is nothing but information inside the component.

If you forget that the state variable updates on the next rendering, you might try to read the state variable right after changing it. Unfortunately, it won't work. Suppose we update date of birth at some point inside some function. Don't update age right after it. remember it happens asynchronously so age will be calculated using the previous Date of birth value. ==don't use state value to change value if possible right after it changes== 
```
setState(newValue)
console.log(state) // the value of state is still oldValue
```
### use Effect
Before understanding the use Effect, we need to understand mounting and unmounting of the component. Mounting means when the component is being inserted into the DOM for first time.

It is just abstraction for `ComponentDidMount`, `ComponentUpdate` and `ComponentUnMount`.

The child of `useEffect` run first than parent. React follows bottom-up approach while using `useEffect`

Clean Up function is extremely necessary, it runs before executing `useEffect` another time.
### use Callback
Sometimes we send functions as props. React treats that function as new prop everytime component re-renders even if function itself hasn't changed. `useCallback` is used to avoid rendering again.
Tips: 
1. Don't overuse it.
### use Memo
Sometimes when Parent Re-renders but children have same props. then it is no need to render that child component, right ? Exactly. Why does Child Re-renders ? Cuz props is an object and in js no object are same remember ? But we don't need to render that child component. So `useMemo` is used. Another use is we can memoize values also. Like we want to calculate the value of derived variable only if the variable that change the derive variable change. It can be confusing but main use it to memoize component and any value.
Tips: 
1. Don't overuse it.
### use Ref

### use Transition
### 

