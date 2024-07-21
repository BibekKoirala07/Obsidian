```
import mongoose from 'mongoose';

const { Schema } = mongoose;

const blogSchema = new Schema({

  _id: false, // we can also disable _id

  title: String, // String is shorthand for {type: String}
  
  comments: [{ body: String, date: Date }],
  
  date: { type: Date, default: Date.now },
  
  hidden: Boolean,
  
  meta: {
    votes: Number,
    favs: Number
  }
  
});

const Model = mongoose.model("model", blogSchema)
module.exports = Model
```


### mongoose  _id
##### We can save our own _id and even save the document without _id
```

const schema = new Schema({
  _id: Number // <-- overwrite Mongoose's default `_id`
});
const Model = mongoose.model('Test', schema);

const doc = new Model();
await doc.save(); // Throws "document must have an _id before saving"

doc._id = 1;
await doc.save(); // works
```


### Methods
- While declaring methods, don't use ESM as it prevent `this`
```javascript
animalSchema.methods.findSimilarTypes = function(cb) {
  return mongoose.model('Animal').find({ type: this.type }, cb);
};
```
- Now we have findSimilarTypes for any instance of `Animal` model
```javascript
const Animal = mongoose.model('Animal', animalSchema);
const dog = new Animal({ type: 'dog' });

dog.findSimilarTypes((err, dogs) => {
  console.log(dogs); // woof
});
```
- Overwriting a default mongoose document method may lead to unpredictable results. See [this](https://mongoosejs.com/docs/api/schema.html#schema_Schema-reserved) for more details. 
![[Pasted image 20240601105812.png]]

### Statics
- It works on Model directly. We use `this` and use function like `findOneAndUpdate`, `find`, `create` and other function in it 
- don't use arrow function as it prevent explicitly binding `this`
```javascript
this.create({email, password})
```
