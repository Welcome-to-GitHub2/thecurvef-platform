# Self Security Review – TheCurveF Platform

## Objective

To perform a structured security assessment of the platform by simulating realistic misuse scenarios, validating defensive controls, and documenting operational findings.

This review was conducted in a controlled environment from a defensive perspective to improve system resilience and developer security awareness.

---

# 1. Input Validation & Injection Testing

## 1.1 Stored Cross-Site Scripting (XSS) Test

### Test Performed
Submitted the following payload via the registration form:

```html
<script>alert("XSS")</script>
```

### Observed Behaviour
- Payload stored in database
- Rendered in admin dashboard as plain text
- No JavaScript execution occurred
- No browser alert triggered

### Security Controls Verified
- React automatic output escaping
- No use of `dangerouslySetInnerHTML`
- No raw DOM injection
- No reflected execution in browser

### Risk Assessment
Low — Output encoding functioning correctly.

---

## 1.2 Injection Simulation (SQL & NoSQL Style Payloads)

### Payload 1 – SQL-style
```
' OR 1=1 --
```

### Payload 2 – NoSQL-style
```json
{"$ne": null}
```

### Test Objective
To determine whether:
- Backend interprets operators
- MongoDB query logic can be manipulated
- Data is treated as executable query input

### Observed Behaviour
- Payload stored as plain string
- No query manipulation occurred
- No abnormal database behaviour
- API returned `200 OK`
- Admin dashboard displayed raw string safely

### Security Controls Verified
- Controlled schema insertion
- No dynamic query construction
- No direct operator injection
- Structured request handling

### Risk Assessment
Low — Injection-style payloads treated as string input.

---

# 2. HTTP Method & API Handling Validation

## 2.1 Invalid POST Body

### Test
Manually submitted incomplete JSON:

```json
{ "formType": "registration" }
```

### Result
- Server returned `400 Bad Request`
- No database write occurred
- No crash observed

---

## 2.2 Invalid HTTP Method (GET on POST Route)

### Test
Sent a `GET` request to:

```
/api/submit-form
```

### Result
- Server returned `405 Method Not Allowed`
- Correct RESTful behavior enforced
- No unexpected exposure

### Risk Assessment
Low — API method validation functioning correctly.

---

# 3. Secrets & Environment Variable Protection

## 3.1 Hardcoded Secrets Search

### Test Performed
Searched codebase for:
- `mongodb+srv`
- `process.env`
- API keys
- Connection strings

### Result
- No hardcoded secrets found
- All sensitive values loaded via environment variables

---

## 3.2 .env File Protection Verification

### Checks
- `.env.local` confirmed present locally
- `.env.local` confirmed excluded via `.gitignore`
- `git status` confirmed not tracked
- Repository contains no exposed credentials

### Risk Assessment
Low — Proper separation of configuration and source code.

---

# 4. Deployment Security Review (Production – Vercel)

Production URL:
https://thecurvef.co.za

## 4.1 HTTPS Enforcement

### Observed
- HTTP requests redirect to HTTPS (307 redirect)
- Strict transport security enabled

Verified Headers:
- `Strict-Transport-Security`
- `X-Frame-Options: DENY`
- `X-Content-Type-Options: nosniff`
- `Referrer-Policy`
- `Permissions-Policy`

### Risk Assessment
Low — Secure transport correctly enforced.

---

## 4.2 Admin Route Exposure Observation

### Route Tested
```
/admin
```

### Observed Behaviour
- Publicly accessible
- Returns `500 Internal Server Error`
- No authentication protection implemented
- Server-side exception triggered

### Security Finding
The admin route is not protected by access control logic and does not gracefully deny unauthorized access.

### Recommended Improvement
- Implement authentication middleware
- Restrict route access to authorized users
- Return `401 Unauthorized` or `403 Forbidden` instead of `500`

### Risk Level
Medium — Access control hardening required before scaling.

---

# 5. MongoDB Atlas IP Restriction Incident

## Issue

Form submissions began returning:

- `500 Internal Server Error`
- `MongoServerSelectionError`
- TLS handshake failure

## Root Cause

MongoDB Atlas temporary IP whitelist rule (`0.0.0.0/0`) expired after its configured duration.

Backend was unable to establish a TLS connection to the database cluster.

## Impact

- Registration system temporarily unavailable
- Database connectivity failure
- Production 500 errors

## Resolution

- Re-added IP address in MongoDB Atlas Network Access
- Verified SRV connection string
- Restarted application
- Confirmed successful `200 OK` submission
- Verified database write operation

## Security Insight

Temporary IP whitelisting reduces attack surface but requires monitoring to prevent unexpected downtime.

Recommended improvements:
- Implement monitoring alerts
- Use controlled IP ranges
- Add database connectivity health checks

---

# Conclusion

TheCurveF platform demonstrates:

- Secure input handling
- Proper output encoding
- Protection against injection-style payloads
- Correct REST method enforcement
- Secure secrets management
- HTTPS enforcement in production
- Real-world operational incident handling

Security posture is actively reviewed and iteratively improved through hands-on testing and documented evidence.

This review reflects practical defensive testing performed during development and deployment stages.
