###### `createJsonWebToken.js`
```
const jwt = require("jsonwebtoken");
const createJsonWebToken = async (_id) => {
	return jwt.sign({ _id}, process.env.JWT_SCRET, {expiresIn: "5d"})
}
```
###### `isIdValid.js
- Check whether id is legit or not
```
const mongoose = require("mongoose");
if (!mongoose.Types.ObjectId.isValid(id)) {
    res.status(200).json({ status: "no such id exist" });
}
````
