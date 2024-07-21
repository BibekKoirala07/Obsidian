#### Save Hook
```javascript
schema.pre("save", function(next) {

	return next(); // write return such that parts below it willn't run
})
```