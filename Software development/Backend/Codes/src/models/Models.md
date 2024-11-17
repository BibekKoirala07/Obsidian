```
const mongoose = require("mongoose"); // Erase if already required
const validator = require("validator");
const bcrypt = require("bcrypt");

const userSchema = new mongoose.Schema(
  {
    username: {
      type: String,
      required: true,
      unique: [true, "Your username is not unique"],
    },
    email: {
      type: String,
      required: true,
      unique: [true, "Your Email is already registered"],
    },
    creator: {
      type: Object,
    },
    mobile: {
      type: String,
      required: true,
    },
    password: {
      type: String,
      required: true,
    },
    photo: {
      type: String,
      default:
        "https://th.bing.com/th/id/OIP.tmgWGdzGFmIwg1iaqCbSvgHaHa?w=179&h=194&c=7&r=0&o=5&pid=1.7",
    },
  },
  {
    timestamps: true,
  }
);

//Export the model
const UserModel = mongoose.model("User", userSchema);
module.exports = UserModel

 ``` 

- **RegisterStatics**
```
userSchema.statics.registerStatics = async function (
  username,
  email,
  password,
  mobile,
  photo
) {
  console.log(username, email, password, mobile);

  if (!username || !email || !password || !mobile) {
    throw Error("Please fill in all fields");
  }

  // check whether Email is already registered
  const isAlreadyRegistered = await this.findOne({ email });
  if (isAlreadyRegistered) {
    throw Error("You already have registered. Please login to continue");
  }

  // check whether username is unique or not
  const isUsernameUnique = await this.findOne({ username });
  if (isUsernameUnique) {
    throw Error("You must have unique Username");
  }

  // check whether mobile is unique or not
  const isMobileUnique = await this.findOne({ mobile });
  if (isMobileUnique) {
    throw Error("Your mobile number should be unique");
  }

  // check whether email is valid or not
  if (!validator.isEmail(email)) {
    throw new Error("Invalid email address");
  }

  // Check if the username contains only alphanumeric characters and underscores
  // if (!validator.isAlphanumeric(username, "en-US")) {
  //   throw new Error(
  //     "Username should contain only letters, numbers, or underscores"
  //   );
  // }

  // Check if the username is at least 3 characters long
  if (!validator.isLength(username, { min: 3 })) {
    throw new Error("Username should be at least 3 characters long");
  }

  // Check if the mobile number is a valid phone number
  // if (!validator.isMobilePhone(mobile, "en-US")) {
  //   throw new Error("Invalid mobile number");
  // }

  // check whether password is strong or not
  if (!validator.isStrongPassword(password)) {
    throw Error("Weak Password");
  }
	
  const salt = await bcrypt.genSalt(10); // generate salt
  const hash = await bcrypt.hash(password, salt); // generate hash

  // create the document in database
  const user = await this.create({
    username,
    email,
    password: hash,
    mobile,
  });
  
  return user;
};

```

- **LoginStatics**
```

userSchema.statics.loginStatics = async function (email, password) {

  // checks whether all credentials are given or not
  if (!email || !password) {
    throw new Error("Please enter all credentials");
  }

  // check whether email is valid or not
  if (!validator.isEmail(email)) {
    throw new Error("Invalid email address");
  }

  // check whether user is previously registered or not
  const user = await this.findOne({ email });
  if (!user) {
    throw new Error("You haven't registered yet");
  } 

  // checks whether password is correct or not
  const passwordMatch = await bcrypt.compare(password, user.password);
  if (!passwordMatch) {
    throw new Error("Password doesn't match");
  }
  
  return user;
};

```

### Just a extra Code 
```

const nameSchema = new mongoose.Schema({

  firstName:{
	type: String,
	minlength: 3,
	maxlength: 100,
	enum: ["Books", "Clothing"],  // fixed amount of value
	match: // it is way too complex to ,
	unique: true || false // applies to other too,
	lowercase: ..
  }
  
  age: {
	type: Number,
	min: 0,
	max: 100,
	required: true || false // applies to other too
  }
  
  birthdate: Date, min, max
  
  isPublic: Boolean,
  
  author: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'User', // The ref option specifies that the "author" field should reference           documents in the "User" collection.
  }

  tasks: {
    type: [String] like ["Bibek", "Koirala"] // the difference with enum is that in             enum it is unchangable while at [String] it is changeable
},

}, {

	Strict: false || true // A boolean that controls whether Mongoose should ignore       fields not defined in the schema. If set to true, only fields explicitly defined in     the schema are allowed. If set to false, additional fields not defined in the schema are allowed.,
	validateBeforeSave: true || false //A boolean that determines whether validation should be performed before saving.,
	timeStamps: true || false,
	collection: "My collection" // name of collection
});

```
