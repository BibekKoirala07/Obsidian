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