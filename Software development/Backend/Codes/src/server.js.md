The main function is to start the server and connect to external services such as database and cloud storage.
```
require('dotenv').config({
  path: './config/config.env',
}) 

const connectDB = require("./db/connectDB"); 

const app = require("./app"); 

const PORT = process.env.PORT || 3000; 


connectDB(process.env.MONGO_URI)
  .then(() => {
    const server = app.listen(PORT, () => {
      console.log(`Server is running on http://localhost:${PORT}`);
    });

    process.on("uncaughtException", (err) => {
      console.error(`Error: ${err.message}`);
      console.log("Shutting down the server due to Uncaught Exception");
      process.exit(1);
    });

    process.on("unhandledRejection", (err) => {
      console.error(`Error: ${err.message}`);
      console.log("Shutting down the server due to Unhandled Promise Rejection");

      server.close(() => {
        process.exit(1);
      });
    });
  })
  .catch((error) => {
    console.log("Error while connecting to database:", error);
  });

```