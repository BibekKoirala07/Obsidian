API-Headers are the meta-data that provides information about request or response.
### Common Req/Res Headers
They are key-value pair sent from client to server to provide extra information about request. Here are some type of request-headers: 
##### ==Content-Header== :  Request/Response 
It specifies the media type/format of the request that is being sent to the server. It merely indicates the expected format. 
###### How to set in Client
```
fetch('', {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json' // Specify JSON data
        'Content-Type': 'multipart/form-data' // used for file uploads
        'Content-Type': 'text/html' // used for file uploads
    },
    body: JSON.stringify({ key: 'value' }) // Data to send
});

```
###### How to set in Server
```
app.post('', (req, res) => {
    res.setHeader('Content-Type', 'application/json'); 
              // Specify JSON response
    res.send(JSON.stringify({ message: 'Data received' }));
});

```
###### What if set incorrectly
If set incorrectly, then server will anyhow tries to parse the data but it may do it incorrectly. so it responds with `415 unsuppoted media type` error response.
##### ==Accept== : Request
It specifies the format/media-type the client want the response in. It merely indicates the format that client want.
###### How to set in Client
```
fetch('https://api.example.com/data', {
    method: 'GET',
    headers: {
		'Accept': 'application/json' // Specify that we want JSON response
		'Accept': 'text/html' //indicates the client expects an HTML response.
		'Accept': '*/*' // indicates client accept any type
});
```
###### What if set incorrectly
Nothing. It is just the preference that users want so that user can use the gotten data in efficient ways. Despite mentioning, server may still responds with different format
##### ==User-Agent== : Request
The `User-Agent` header provides information about the client (browser, app, etc.) making the request, including details about the software and version.
###### How to set in Client
```
headers: { 'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64)                         AppleWebKit/537.36 (KHTML, like Gecko) Chrome/92.0.4515.107                   Safari/537.36' // Example User-Agent string for Chrome                        browser on Windows  
 }
```

> [!NOTE] For Some JavaScript environments
> Some JavaScript environments (like browsers) don't allow you to set the `User-Agent` header manually due to security reasons. In such cases, it is automatically set by the browser itself.
###### How to read Server
```
app.get('/data', (req, res) => {    
	const userAgent = req.headers['user-agent']; // Access User-Agent from                 request   
	console.log(userAgent); // Logs User-Agent info, useful for analytics or               feature detection   
	res.send('User-Agent logged'); })
```
###### What if set incorrectly
- **If Not Set or Misleading**: If the `User-Agent` is incorrect or missing, the server may not serve the right content or optimized version based on the client. For example, the server might send a mobile-optimized version to a desktop browser if the `User-Agent` is set incorrectly.
- **Insecure User-Agent Spoofing**: Spoofing the `User-Agent` could mislead the server into delivering content meant for a different client, but this does not pose a security risk by itself unless used maliciously in conjunction with other factors.

### Security-Related Headers
##### ==Strict Transport Strictly== : Response
It tell the browsers to enforce `https` in future request. It instructs the browser to communicate using `https` with server which prevents the man in the middle of the attacks and have secure connections. It tells browser to automatically convert all future requests to same origin from `http` to `https` for a specified period. (defined by `max-age`)
###### Server
```
app.use((req, res, next) => {
    res.setHeader('Strict-Transport-Security', 'max-age=31536000;                             includeSubDomains'); // Set HSTS
    next();
});

```
`max-age` means for how much will this header work. Here, `31536000` means use `https` protocol for that duration i.e. 1 year. `includeSubDomains` means use same for sub-domains like `/auth` or `/settings` or more routes
###### What if not set or set correctly
If not set, then client will communicate with server with `http` protocol. This can have less secure connection. 

> [!important] What it doesn't do
> It doesn't set the protocol to `https`. It merely suggest the client to use secure protocol
##### ==Content-Security-Policy== : Response

### Custom Headers
### Trouble Shooting Common Headers Issue