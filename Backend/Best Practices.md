Here are the some best practices that should be followed while making API
- Data validation should be done before reaching application controllers
- Rate limiting should be implemented as we need to protect our API by making register and logging process time longer
- Logging should be done using `winston` or `morgan`
- `Helmet.js` should be used.
- Compression can be done using `compression` library available in express. It is a middleware.