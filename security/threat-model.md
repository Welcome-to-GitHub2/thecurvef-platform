# Threat Model – TheCurveF Platform

## Objective

This document outlines potential security threats against the platform and mitigation strategies applied.

---

## 1. Threat: Form Submission Abuse (Spam / Bot Attacks)

### Risk
Attackers may attempt to:
- Flood the form endpoint
- Inject malicious scripts
- Send automated spam requests

### Mitigation
- Server-side validation
- Input sanitization
- Structured error handling
- Controlled API response format

---

## 2. Threat: Injection Attacks (NoSQL / Script Injection)

### Risk
Malicious input attempting:
- NoSQL injection
- Script injection
- Database manipulation

### Mitigation
- Strict schema design
- Sanitized request payloads
- No direct query construction from raw input
- Environment variables used for DB credentials

---

## 3. Threat: Environment Variable Exposure

### Risk
Leaking:
- MongoDB connection strings
- API secrets

### Mitigation
- All secrets stored in Vercel environment variables
- No credentials committed to repository
- .env excluded via .gitignore

---

## 4. Threat: API Endpoint Enumeration

### Risk
Attackers discovering:
- Hidden routes
- Misconfigured endpoints

### Mitigation
- Minimal exposed routes
- Controlled method handling (GET/POST only where necessary)
- Consistent error responses to avoid information leakage

---

## 5. Threat: Data Privacy Non-Compliance

### Risk
Violation of user data protection laws (POPIA awareness).

### Mitigation
- Minimal data collection
- Clear purpose limitation
- No unnecessary storage
- Controlled database access

---

## Security Philosophy

Security is implemented as a design principle, not as an afterthought.
The system was developed with defensive thinking and proactive risk identification.
