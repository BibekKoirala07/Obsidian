###### Virtuals are document properties that we can set or get but aren't stored in mongoDB.

```javascript
// That can be done either by adding it to schema options:
const personSchema = new Schema({
  name: {
    first: String,
    last: String
  }
}, {
  virtuals: {
    fullName: {
      get() {
        return this.name.first + ' ' + this.name.last;
      }
    }
  }
});

// Or by using the virtual method as following:
personSchema.virtual('fullName').get(function() {
  return this.name.first + ' ' + this.name.last;
});
```
###### Now mongoose will call getter function everytime we access `fullName` property
```javascript
console.log(axl.fullName); // Axl Rose
```