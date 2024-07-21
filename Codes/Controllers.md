- Check whether id is legit or not
```
if (!mongoose.Types.ObjectId.isValid(id)) {
    res.status(200).json({ status: "no such id exist" });
}

const createJsonWebToken = async (_id) => {
return jwt.sign({ _id}, process.env.JWT_SCRET, {expiresIn: "5d"})
}
```

- **Register**
```
const register = async (req, res) => {
  const { username, email, password, mobile } = req.body;

  try {
    // One way
    // const user = new userModel({ username, email, password, mobile });
    // await user.save();

    // Another way to create a collection
    const user = await userModel.registerStatics(
      username,
      email,
      password,
      mobile
    );
    const token = createJsonWebToken(user._id);

    // We don't want to send the password to user so
    const { password: hashedPassword, ...rest } = user._doc;

    // adding expiry date for token, we need to sign in every time we request for anything
    // api, so we use expires feautres to use that same token for certain time
    const expiryDate = new Date(Date.now() + 360000); // for 1 hour
    // return res.status(200).json(user);
    res
      .cookie("token", token, {
        // httpOnly: true, // client won't be accesss the sent cookie
        // when does it expires, it denotes that, alternate property is maxAge
        secure: false,
        expires:
          expiryDate /* there are other too like signed, secure, overwrite etc*/,
      })
      .status(200)
      .json(rest);
  } catch (error) {
    console.log("error", error);
    return res.status(400).json({ status: "error", error: error.message });
  }
};
```

- **Login**
```
const login = async (req, res) => {
  const { email, password } = req.body;
  try {
    const user = await userModel.loginStatics(email, password);
    const token = createJsonWebToken(user._id);
    const { password: hashedPassword, ...rest } = user._doc;
    return res
      .cookie("token", token, { httpOnly: true, secure: false })
      .status(200)
      .json(rest);
  } catch (error) {
    return res.status(400).json({ status: "error", error: error.message });
  }
};
```

- **Social Register**
```
const socialRegister = async (req, res) => {
  let { username, email, phone, photo, social } = req.body;
  if (!phone) {
    phone = "900";
  }

  console.log("it is in register social");

  console.log(username, email, phone, photo);
  const password = generateHashedPassword();
  try {
    let isAlreadyRegistered;
    if (social == "twitter") {
      isAlreadyRegistered = await UserModel.findOne({ username });
    } else {
      isAlreadyRegistered = await UserModel.findOne({ email });
    }
    console.log(isAlreadyRegistered);
    if (isAlreadyRegistered) {
      throw Error("You are already registered");
    }

    let user;
    if (social) {
      if (!email) {
        email = `random${Math.floor(Math.random() * 10000)}@gmail.com`;
        console.log("email", email);
      }
      user = await UserModel.create({
        username,
        email,
        phone,
        photo,
        password,
      });
    } else {
      username = username + `${generateRandomPassword(5)}`;
      user = await UserModel.create({
        username,
        email,
        phone,
        photo,
        password,
      });
    }
    const token = generateJwtToken(user._id);
    const { password: hashedPassword, ...rest } = user._doc;
    res.cookie("token", token, { expiresIn: "1h" });
    return res.status(200).json(rest);
  } catch (error) {
    return res.status(400).json({ error: error.message });
  }
};
```

- **SocialLogin**
```
const socialLogin = async (req, res) => {
  const { social, email, username } = req.body;
  console.log(social, email, username);
  try {
    let user;
    if (social) {
      user = await UserModel.findOne({ username });
    } else {
      user = await UserModel.findOne({ email });
    }
    if (!user) {
      throw Error("You aren't registered");
    }
    const token = generateJwtToken(user._id);
    const { password: hashedPassword, ...rest } = user._doc;
    res.cookie("token", token, { expiresIn: "1h" });
    return res.status(200).json(rest);
  } catch (error) {
    return res.status(400).json({ error: error.message });
  }
};
```

- **SignOut**
```
const signOut = async (req, res) => {
  console.log("signout");
  return res.clearCookie("token").status(200).json({ status: "Signed Out" });
};
```

- **DeleteUser**
```
const deleteUser = async (req, res) => {
  console.log("delete");
  const { id } = req.params;

  console.log("req.user", req.user._id, "in params", id);

  // checking whether the _id we got from params is even valid or not
  if (!mongoose.Types.ObjectId.isValid(id)) {
    return res.status(400).json({ error: "No such user" });
  }

  // remember we append the decoded jwt which has _id from cookies? we check       whether that _id is same we got from params as we sent user._id in jwt.sign      and when we decode that jwt we get that _id and we appended 
  if (req.user._id !== id) {
    console.log("other id dodged");
    return res.status(400).json({ error: "You can't delete other's id" });
  }

  try {
    const deletedUser = await userModel.findOneAndDelete(
      {
        _id: id, // passing id as _id
      },
      { new: true }
    );
    console.log("deletedUser", deletedUser)
    return res.status(200).json({ status: "The User is deleted" });
  } catch (error) {
    return res.status(400).json({ error: error.message });
  }
};
```