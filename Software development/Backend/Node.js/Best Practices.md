Here are the some best practices that should be followed while making API : 
- Data validation should be done before reaching application controllers by use of `express validator` or `joi`
- [[Security & Risk Managements#1. Brute Force Attacks | Rate Limiting]] should be implemented as we need to protect our API by making register and login process time longer.
- Logging should be done using `winston` or `morgan`.
- `Helmet.js` should be used.
- Compression can be done using `compression` library available in express. It is a middleware. However it is not done mostly as it is pain in the ass.
- Always implement [[Logging]]
- Know the request time taken by each request using request Logger from [[Logging]].

### The best practices for folders are 
```
/Your-Node-Express-Project
├── /src
│   ├── /controllers        # Controllers: Handle HTTP requests and business logic
│   ├── /models             # Mongoose models (for MongoDB) or Sequelize models (for SQL)
│   ├── /routes              # Route definitions mapping HTTP methods and paths to controllers
│   ├── /middlewares         # Middleware functions (e.g., authentication, logging, error handling)
│   ├── /services            # Business logic and application-specific services
│   ├── /utils               # Helper functions, constants, and utility modules
│   ├── /socket              # WebSocket setup and handlers
│   │   ├── socket.js        # Main WebSocket initialization and setup
│   │   └── socketEvents.js  # Custom events and their handlers
│   ├── /advanced            # More complex utilities and features
│   │   ├── caching.js       # Data caching mechanisms
│   │   ├── validation.js    # Custom validation logic (e.g., using Zod)
│   │   └── logging.js       # Logging setup and configuration
├── /config                  # Configuration files for different environments
│   ├── default.js           # Default configuration
│   ├── production.js        # Production environment settings
│   ├── development.js       # Development environment settings
│   └── test.js              # Testing environment settings
├── /public                  # Static files accessible by the client
│   ├── /css                 # Stylesheets
│   ├── /images              # Image files
│   ├── /js                  # JavaScript files
│   └── index.html           # Main entry point of the web application
├── /logs                    # Log files
│   ├── app.log              # General application logs
│   └── errors.log           # Logs for errors and exceptions
├── /tests                   # Testing directories
│   ├── /unit                # Unit tests
│   ├── /integration          # Integration tests
│   └── /e2e                 # End-to-end tests
├── /scripts                 # Miscellaneous scripts
│   ├── deploy.js            # Deployment script
│   └── migrate.js           # Database migration script
├── .env                     # Environment variables
├── .gitignore               # Files and directories to ignore in version control
├── package.json             # NPM configuration file
├── app.js                   # Application entry point and setup
└── server.js                # Server initialization and start
```