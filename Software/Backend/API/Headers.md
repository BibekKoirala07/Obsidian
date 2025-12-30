### From Frontend
```
{
  // Content Headers
  "Content-Type": "application/json", // Specifies the type of data sent in the request. Other values: "application/x-www-form-urlencoded", "multipart/form-data"
  "Accept": "application/json", // Specifies the type of data the client expects to receive. Other values: "text/html", "text/plain", "application/xml"
  
  // CORS Headers
  "Access-Control-Allow-Origin": "*", // Sent by the server (but may be influenced by the frontend via credentials). Backend specifies which origins can access the resource
  "Access-Control-Allow-Credentials": "true", // Sent by the server; allows cookies to be included in cross-origin requests (e.g., authentication tokens)

  // Authentication Headers
  "Authorization": "Bearer <token>", // Used to send the authentication token (e.g., JWT) to authenticate the request
  "X-Requested-With": "XMLHttpRequest", // Indicating that the request was made with JavaScript (used mainly for Ajax requests)

  // Custom Headers
  "X-Custom-Header": "CustomValue", // Can be defined for any custom need; often used for app-specific logic or metadata
  "X-Request-ID": "abc123", // Used to track individual requests for logging or debugging

  // Security Headers (Frontend's role is more to handle and respect these, not set them)
  "X-Content-Type-Options": "nosniff", // This is set by the server to prevent MIME sniffing. The frontend ensures to only accept proper MIME types
  "X-Frame-Options": "SAMEORIGIN", // Ensures the page cannot be embedded in an iframe unless from the same origin

  // Language and Localization Headers
  "Accept-Language": "en-US,en;q=0.9", // Specifies the preferred language(s) the client can handle. The server can use this to serve localized content
  
  // User-Agent Header (Usually automatically set by the browser)
  "User-Agent": "YourApp/1.0 (https://yourapp.com)", // Identifies the client application (typically auto-managed by the browser, but can be manually set in JavaScript)
  
  // Cache Control (Frontend may send requests that bypass cache or include cache-specific headers)
  "Cache-Control": "no-cache", // Requests data with no caching. Other values include "max-age", "private", etc.
  
  // Referer Header (Automatically set by browser)
  "Referer": "https://yourdomain.com/page", // Sent automatically by the browser, showing the page where the request originated
}

```
### From Backend
```
{
  // Content Headers
  "Content-Type": "application/json", // Specifies the response format. Other values: "text/html", "text/plain", "application/xml"
  "Content-Length": "1234", // Response body size in bytes. Backend computes dynamically if not set explicitly
  "Content-Encoding": "gzip", // Specifies compression. Other values: "br" (Brotli), "deflate"

  // Cache Control
  "Cache-Control": "no-cache, no-store, must-revalidate", // Disables caching. Other values: "private", "public", "max-age=3600"
  "Pragma": "no-cache", // Legacy HTTP 1.0 cache control. Typically paired with "Cache-Control"
  "Expires": "0", // Indicates the resource is already expired. Use a future date for caching

  // Security Headers
  "X-Content-Type-Options": "nosniff", // Prevents MIME sniffing. No other valid value
  "X-Frame-Options": "DENY", // Prevent embedding in iframes. Other values: "SAMEORIGIN", "ALLOW-FROM https://example.com"
  "Strict-Transport-Security": "max-age=31536000; includeSubDomains", // Enforce HTTPS for a year. Add "preload" for HSTS preload list
  "Content-Security-Policy": "default-src 'self'; script-src 'self' https://example.com", // Restricts resource loading. Add specific sources as needed

  // CORS Headers
  "Access-Control-Allow-Origin": "*", // Allows all origins. Replace with specific origin like "https://example.com" for security
  "Access-Control-Allow-Methods": "GET, POST, PUT, DELETE, OPTIONS", // Specify allowed HTTP methods
  "Access-Control-Allow-Headers": "Content-Type, Authorization, X-Custom-Header", // Whitelist allowed headers
  "Access-Control-Allow-Credentials": "true", // Indicates if cookies/credentials can be included in cross-origin requests

  // Custom Headers
  "X-Custom-Header": "CustomValue", // Backend-defined custom header for application-specific logic
  "X-App-Version": "1.0.0", // Example custom header for app versioning

  // Other Common Headers
  "Accept-Language": "en-US,en;q=0.9", // Indicates client's preferred language(s). Backend may serve localized content
  "User-Agent": "YourApp/1.0 (https://yourapp.com)", // Identifies the client making the request
  "Referer": "https://yourdomain.com/page", // Backend can track the source page that triggered the request
  "X-Forwarded-For": "123.123.123.123", // Client IP when behind a proxy or load balancer. Useful for logging and analytics
  "ETag": "W/\"12345-67890\"", // Unique identifier for a specific resource version. Used for caching validation
  "Last-Modified": "Wed, 21 Dec 2022 07:28:00 GMT" // Timestamp of the last resource update
}

```