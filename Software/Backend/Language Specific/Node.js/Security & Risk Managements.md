There are number of attacks that can happen in our APIs. Some of them are :
### 1. Brute Force Attacks
This normally means that attacker tries to guess the password by trying millions times. It can be prevented by 
1. Using `brcyptjs` as it slows down the login process. 
2. By implementing rate limiting from same IP address using `express-rate-limit`
3. By implementing maximum login attempts like if many times user types wrong password then make the user wait for some duration like 1 hour before user could try next time
4. By locking in account after some incorrect trials
5. By activating 2FA authentication
6. Strong Password Policies
7. CAPTCHA implementation
### 2. Denial of Services Attacks
This means that hacker does huge amount of request in short amount of time, crashed the servers and application becomes unavailable. We can prevent it by 
1. **Rate limiting**
2. limit the amount of data that can be sent in `req.body` so that servers don't crashes. It can be in `app.use({express.json: 10kb})`
3. **Avoid Evil Regular Expression:** i.e. the type of R.E. that takes huge amount of time to come to result.
4. **Load Balancing**: Use load balancers to distribute traffic across multiple servers.
5. **Caching**: Implement caching strategies (e.g., using Redis) to reduce load on the server for frequently accessed data.
6. **Web Application Firewalls (WAF)**: Consider using a WAF to filter and monitor HTTP traffic.
### 3. SQL Injection
**Attack**: Attackers exploit vulnerabilities in your SQL queries to manipulate the database.
**Prevention**:
- **Parameterized Queries**: Use parameterized queries or prepared statements to prevent SQL injection.
- **ORMs**: Use an ORM (like Sequelize or TypeORM) that automatically sanitizes inputs.
- **Input Validation**: Validate and sanitize inputs to ensure they conform to expected formats.
### 4. Cross-Site Scripting (XSS)
**Attack**: Attackers inject malicious scripts into web applications viewed by other users.
**Prevention**:
- **Output Encoding**: Encode data before rendering it to prevent execution of injected scripts.
- **Content Security Policy (CSP)**: Implement a CSP to restrict the sources from which scripts can be loaded.
- **Input Sanitization**: Sanitize user input to remove potentially dangerous content.
### 5. Session Hijacking
**Attack**: Attackers gain unauthorized access to a user's session.
**Prevention**:
- **Secure Cookies**: Use secure cookies with the `HttpOnly` and `Secure` flags.
- **Regenerate Session IDs**: Regenerate session IDs after login and at regular intervals.
### 6. Cross-Site Request Forgery (CSRF)
**Attack**: Attackers trick a user into submitting a request they did not intend to make.
**Prevention**:
- **CSRF Tokens**: Use CSRF tokens for state-changing requests. Verify the token on the server.
- **Same Site Cookies**: Set cookies with the `SameSite` attribute to prevent them from being sent along with cross-origin requests.