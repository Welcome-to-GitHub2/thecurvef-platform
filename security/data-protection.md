# Data Protection & Privacy Considerations – TheCurveF Platform

## Context

TheCurveF platform collects limited personal information for educational service coordination.

As the platform operates within South Africa, data handling was designed with POPIA (Protection of Personal Information Act) awareness.

---

## 1. Data Minimization

Only essential information is collected:

- Name
- Contact details
- Academic-related information (where applicable)

No unnecessary personal data is requested.

---

## 2. Purpose Limitation

Collected data is used strictly for:

- Responding to inquiries
- Coordinating tutoring sessions
- Administrative communication

Data is not sold, shared, or used for unrelated purposes.

---

## 3. Secure Storage

Data is stored using:

- MongoDB Atlas (cloud-hosted)
- Environment-variable secured credentials
- Restricted database user permissions

No credentials are exposed in source code.

---

## 4. Transmission Security

The platform is deployed over HTTPS via Vercel.

All data transmission between client and server is encrypted.

---

## 5. Access Control Awareness

Administrative access is limited to authorized personnel.

Sensitive credentials are not stored in public repositories.

---

## 6. Future Improvements Identified

Security roadmap considerations include:

- Role-based access controls
- Encrypted backups
- Data retention policies
- Automated monitoring for anomalous activity

---

## Design Philosophy

User trust is critical in education platforms.

Security and privacy were considered during system design rather than treated as an afterthought.
