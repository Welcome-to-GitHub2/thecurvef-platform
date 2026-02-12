# Self Security Review – TheCurveF Platform

## Objective

To perform a structured self-review of the platform’s security posture by identifying potential vulnerabilities, validating defensive controls, and documenting observed risks and remediation actions.

This review was conducted from a defensive and ethical perspective, simulating realistic misuse scenarios while ensuring no production data was compromised.

---

## 1. Input Validation & Injection Testing

### Scope
### Testing focused on identifying:
- Stored Cross-Site Scripting (XSS)
- Malformed payload handling
- Injection-style input manipulation
- Backend validation robustness
- Client-to-server data handling integrity

## 1.1 Stored XSS Validation

### Test Perfomed
Submitted the following payload via the registration form: 
    <script>alert("XSS")</script>

### Observed Behaviour 
- Payload successfully stored in database
- Rendered in admin dashboard as plain text
- No JavaScript execution occurred
- No browser alert triggered
- Client-to-server data handling integrity

### Security Controls Verified
- No browser alert triggered
- React automatic output escaping
- No use of dangerouslySetInnerHTML
- No raw DOM injection

### Risk Assessment 
Low — Output encoding functioning correctly.

---
### Mitigation in Place:
- Strict server-side validation
- Structured request schema enforcement
- No direct database query exposure
- Proper API response handling

---

## 1.2 Injection Simulation (SQL & NoSQL Style Payloads)
### Objective
To validate that backend logic does not interpret user-supplied input as executable query operators.

---

### Test A - SQL-Style Injection
Payload submitted in Full Name field: ' OR 1=1 --

### Expected Risk (Traditional SQL Systems)
If raw SQL concatenation were used, this payload could:
- Bypass authentication
- Manipulate query logic
- Return unintended database records

### Observed Behaviour
- Payload logged in terminal exactly as submitted
- Stored in database as plain string
- Rendered in admin dashboard without execution
- API returned 200 OK
- No query manipulation occurred

### Conclusion
Application is not vulnerable to classic SQL injection patterns. No dynamic SQL query construction detected.

---

### Test B – NoSQL Injection Attempt (MongoDB Operator Injection)
Payload submitted: {"$ne": null}

### Expected Risk 
If backend query filters were constructed directly from request body input (e.g., find({ fullName: req.body.fullName })), MongoDB could interpret $ne as an operator instead of string data.

This could potentially bypass logic constraints.

### Observed Behaviour
- Payload logged as literal string in terminal
- Stored in MongoDB as plain text
- Displayed normally in admin dashboard
- No abnormal filtering behaviour observed
- API returned 200 OK

### Conclusion
User input is treated strictly as data, not executable query operators.
Current backend logic uses insert-based handling rather than dynamic filtering, significantly reducing injection risk.

---

## 2. API Abuse & Endpoint Restriction Testing

### Objective

To evaluate backend resilience against improper API usage, malformed requests, and unauthorized HTTP methods.

Testing was performed using PowerShell and direct HTTP requests to simulate external client behavior outside the browser UI.

---

### 2.1 Missing Required Fields (Validation Enforcement)

#### Test Performed

Sent an incomplete POST request directly to the API endpoint:
Invoke-RestMethod -Uri http://localhost:3000/api/submit-form
-Method POST
-ContentType "application/json" `
-Body '{"formType":"registration"}'


#### Expected Behavior

The API should reject incomplete payloads and prevent database interaction.

#### Observed Result

- Server returned **400 Bad Request**
- No database entry was created
- Admin dashboard remained unchanged
- No unhandled exceptions exposed to client

#### Security Control Verified

- Server-side validation enforced
- Required fields validated before database interaction
- Backend rejects malformed or incomplete submissions

#### Risk Assessment

Low — Input validation functioning correctly.

---

### 2.2 Invalid HTTP Method Restriction

#### Test Performed

Attempted to access the submission endpoint using an unsupported HTTP method:
Invoke-RestMethod -Uri http://localhost:3000/api/submit-form
-Method GET


#### Expected Behavior

Endpoint should reject unsupported HTTP methods.

#### Observed Result

- Server returned **405 Method Not Allowed**
- No data exposure occurred
- No database interaction triggered

#### Security Control Verified

- Endpoint strictly restricted to POST requests
- Reduced attack surface
- Proper HTTP method enforcement

#### Risk Assessment

Low — Route handling correctly configured.

---

### 2.3 Payload Manipulation via DevTools

#### Test Performed

Modified request payload using browser Network tab to attempt:

- Changing boolean values
- Editing request body fields
- Tampering with expected input structure

#### Observed Result

- Server maintained validation rules
- Unauthorized modifications rejected or normalized
- No privilege escalation or schema corruption observed

#### Security Control Verified

- Backend does not trust client-side input
- All validation enforced server-side

---

## 3. Environment Variable & Secrets Management Review

### Test Performed:
- Verified repository contains no secrets
- Confirmed `.env` file is excluded from version control
- Checked for hardcoded credentials in source files

### Result:
- All credentials secured via environment variables
- No exposed API keys or connection strings
- Proper separation between development and production configuration

---

## 4. Deployment Security Review

Platform deployed via Vercel.

### Checks Performed:
- HTTPS enforced by default
- No debug endpoints publicly exposed
- No publicly accessible admin routes
- Production environment variables managed securely

### Result:
Deployment aligns with secure-by-default cloud configuration practices.

---

## 5. MongoDB Atlas IP Restriction Incident (Documented Operational Event)

### Issue

Form submission began returning:

- `500 Internal Server Error`
- `MongoServerSelectionError`
- TLS handshake failure

### Root Cause

The MongoDB Atlas temporary IP whitelist rule (`0.0.0.0/0`) expired after its one-week duration.

As a result, the backend API could not establish a secure TLS connection to the MongoDB cluster.

### Impact

- Backend API unable to connect to database
- Registration system temporarily unavailable
- Users experienced 500 errors on submission

### Resolution

- Re-added IP address to MongoDB Atlas Network Access
- Verified SRV connection string configuration
- Restarted development server
- Confirmed successful `200 OK` response
- Validated successful database write operation

### Security Insight

Temporary IP whitelisting enhances security by limiting attack surface.  
However, expiration without monitoring can cause unexpected downtime.

Future improvement:
- Configure persistent IP rules where appropriate
- Implement connection health monitoring
- Add operational alerts for database connectivity failures

---

## 6. Identified Risk Areas (Ongoing Hardening)

- No Web Application Firewall (WAF) configured
- No intrusion detection monitoring
- Limited production logging visibility
- No automated alerting for infrastructure misconfiguration

---

## Conclusion

TheCurveF platform demonstrates:

- Secure API design principles
- Controlled server-side validation
- Environment variable protection
- Secure cloud deployment practices
- Real-world incident handling and root cause analysis

Security improvements are iterative and continuously reviewed as the platform evolves.
