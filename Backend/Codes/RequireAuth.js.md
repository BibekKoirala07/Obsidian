- **In Cookies**
```
const jwt = require("jsonwebtoken");

 // use body parser

const requireAuth = (req, res, next) => {

  // getting token from client 
  const token = req.cookies.token;
  console.log("token in require Auth", token);

  // check whether we got the token or not
  if (!token) {
    return res.status(401).json({ error: "Sorry, Unauthorized Request" });
  }

  // verify the jwt token we got from client
  jwt.verify(token, process.env.SECRET, (err, decoded) => {
    if (err) {
      return res.status(401).json({ error: "Token verification failed" });
    }

	// appending everything to req.user to check further, the decoded mostly           have _id as we have put that in jwt.sign() when we sent that to user. we         get that _id, as we want to update the specific user profile or delete, we       send the id in params and we check that id with jwt decoded id
    req.user = decoded;
    next();
  });
};

module.exports = {
  requireAuth,
};
```

- **In Header**
```
export default async function requireAuth(req, res, next){
    try {
        
        // access authorize header to validate request
        const token = req.headers.authorization.split(" ")[1];

        // retrive the user details fo the logged in user
        const decodedToken = await jwt.verify(token, ENV.JWT_SECRET);

        req.user = decodedToken;

        next()

    } catch (error) {
        res.status(401).json({ error : "Authentication Failed!"})
    }
}

or

const requireAuth = async (req, res, next) => {
  const { authorization } = req.headers;

  if (!authorization) {
    return res.status(400).json({ error: "No token available" });
  }

  const token = authorization.split(" ")[1];

  try {
    const { id } = await jwt.verify(token, process.env.SECRET);
    req.user = await User.findOne({ _id: id }).select("_id");
    next();
  } catch (error) {
    console.log("error", error);
    res.status(404).json({ error: error.message });
  }
};
```