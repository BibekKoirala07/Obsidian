To do but it isn't necessary
- Multiple Log transport like File, Console, or external logging services like Loggly, AWS CloudWatch
- Always rotate the logs
- Asynchronous and Synchronous Logging
- Measure request time using winston
- If possible don't show sensitive data in logging
- Log sampling means only logging required logs of similar logs.
- Tracing correlation across log file.
### winston
#### [[Logging#Code | Go to Code For Single Logger]]
#### [[Logging#Multiple Logger | Go to Code for Multiple Logger]]
#### [[Logging#Request Logger | Request Logger]]
It is to know the time taken by each request.
###### Transport
It is a term to describe where we can send our log files to. It is usually sent to `logs/app.log` or `logs/error.log`
###### Formatting
How the output logs looks like.
###### Log Levels
There are six level, use AI to know that. Some of from my top of head are `warn`, `info`, `silly`, e.t.c.
###### Profiling
It just means that the time that it takes for that code to execute
```
const requestProfiling = (path) => {
	const profiler = paymentLogger.startTimer()

	// your code

	profiler.done({message: `Can do anything here ${path}`})
	
}

requestProfiling("/payment"); // durationMs will be added
```
###### Log Centralization
A place where we send our logs to central service like better stack where further analysis. Use better stack website to know more.
#### Code
###### Single Logger
A code in `src/utis/logger.js` for single Logger
```
const winston = require("winston");
const path = require("path");
// actually winston.format is a object so we have many options

const { combine, json, timestamp, prettyPrint, printf, errors } =
  winston.format;
// printf means that we can print whatever we want like
// print((d) => `${d.info} ${d.message}`) like this
// pretty print means it will give the json but in more pretty format

const logger = winston.createLogger({
  level: "info", // it is a default loggin level,
  // the default one is info and logging below that will not be shown at all
  format: winston.format.combine(
    // this is format means how message will be
    // dispalyed, the default contains level and message that we sent. 
    // in combine we can also add Error to get console.log like error

    errors({ stack: true }),
    timestamp(),
    json()
  ),
  
  // format: winston.format.cli, // this workds only with Console() and it provides colorisation
  // which can be easier to read

  // we can also add our own properties in logger, scroll to bottom to know that
  transports: [
    new winston.transports.Console(), // this is regular console.log
    new winston.transports.File({
      filename: path.join(__dirname, "../../logs/app.log"),
      level: "info", // this means that this log will only be available here
    }),
    new winston.transports.File({
      filename: path.join(__dirname, "../../logs/error.log"),
      level: "error",
    }),
    // winston comes with many custom transport like mongoDB, just type custom
    // winston transport and you will get that.
  ],
});  

module.exports = logger;

const requestLogger = { isAuthenticated: false, method: "GET" };
logger.warn("The message", requestLogger); // we will get two extra properties in log from now
// but this is messy, so we can create child in winston like this
// const childLogger = logger.child(requiestLogger)
// childLogger.warn("Warning"); as usual those two things will be added.

// we can add error stack like this
// childLogger.warn("the error", new Error("504 not found"));
```
###### Multiple Logger
Multiple Logger are also available. May be we have order and payment services, Then we can add those using `winston.loggers.add`.
```
const winston = require("winston");
const path = require("path");

const { combine, json, timestamp, prettyPrint, printf, errors } = winston.format;

winstons.loggers.add("orderLogger", {
  format:
  transports: [
    new winston.transports.Console(), 
  ],
  defaultMeta: {service: "OrderPayment"} // this will automatically be added
})

winstons.loggers.add("PaymentLogger", {
  format:
  transports: [
    new winston.transports.Console(), 
  ],
  defaultMeta: {service: "PaymentLogger"} // this will automatically be added
})
```
Then in app.js we can do this 
```
require("./loggers")

const orderLogger = winston.loggers.get("orderLogger")
const paymentLogger = winston.loggers.get("paymentLogger")

orderLogger.info("An information");
paymentLogger.info("An information"); // they will give differnt outputs

```
###### Production Level Logger
It is absolutely necessary to rotate files daily so that logs don't grow large and cost more money.
```
const winston = require('winston');
require('winston-daily-rotate-file');
const path = require('path');

// Set the environment
const env = process.env.NODE_ENV || 'production';

// Log rotation to prevent huge log files in production
const dailyRotateFileTransport = new winston.transports.DailyRotateFile({
  filename: path.join(__dirname, '../../logs', 'app-%DATE%.log'),
  datePattern: 'YYYY-MM-DD',
  zippedArchive: true,
  maxSize: '20m',
  maxFiles: '14d'
});

// Create Winston logger
const logger = winston.createLogger({
  level: env === 'development' ? 'debug' : 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.errors({ stack: true }), // Log error stack traces
    winston.format.json()
  ),
  transports: [
    dailyRotateFileTransport, // File logging with rotation
    new winston.transports.File({ filename: path.join(__dirname, '../../logs/error.log'), level: 'error' }), // Log errors
    new winston.transports.Console({  // Console log in dev mode
      format: winston.format.combine(
        winston.format.colorize(),
        winston.format.simple()
      ),
      silent: env === 'production' // Disable console logging in production
    })
  ],
  exceptionHandlers: [
    new winston.transports.File({ filename: path.join(__dirname, '../../logs/exceptions.log') }) // Handle uncaught exceptions
  ],
  rejectionHandlers: [
    new winston.transports.File({ filename: path.join(__dirname, '../../logs/rejections.log') }) // Handle unhandled promise rejections
  ]
});

// Export the logger
module.exports = logger;

```
###### Request Logger
```
const logger = require('../utils/logger'); // Your Winston logger configuration

const THRESHOLD = 500; // Set the threshold in milliseconds

const requestLogger = (req, res, next) => {
  const startTime = process.hrtime(); // Start the timer

  res.on('finish', () => {
    const diff = process.hrtime(startTime); // Calculate the duration
    const duration = (diff[0] * 1e3 + diff[1] * 1e-6).toFixed(2); // Convert to milliseconds

    // Create log metadata
    const logInfo = {
      method: req.method,
      url: req.originalUrl,
      status: res.statusCode,
      duration: `${duration} ms`,
      timestamp: new Date().toISOString(),
    };

    // Check if the request took longer than the threshold
    if (duration > THRESHOLD) {
      // Log as a warning if it exceeds the threshold
      logger.warn(`Request exceeded time threshold of ${THRESHOLD} ms`, logInfo);
    } else {
      // Log as info for normal requests
      logger.info(`Request handled in ${duration} ms`, logInfo);
    }
  });

  next(); // Proceed to the next middleware or route handler
};

module.exports = requestLogger;

```
and use Request Logger in production 
```
const app = express();

app.use(requestLogger);

app.get('/', (req, res) => { res.send('Hello, World!'); });
```
