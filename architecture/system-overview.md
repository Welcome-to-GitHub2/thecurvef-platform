# System Architecture Overview

## High-Level Flow

User → Frontend (Next.js) → API Route → MongoDB → Response → User

---

## Components

### 1. Frontend
- Built with Next.js
- Handles user interaction
- Sends validated requests to backend

### 2. Backend API Routes
- Handles POST/GET requests
- Performs server-side validation
- Communicates with MongoDB
- Returns structured JSON responses

### 3. Database
- MongoDB Atlas (cloud-hosted)
- Secure connection string stored in environment variables
- Structured schema for user submissions

### 4. Deployment Layer
- Hosted on Vercel
- Environment variables secured via dashboard
- Separate dev & production configurations

---

## Key Engineering Concepts Applied

- Client-server separation
- API request validation
- Environment variable isolation
- Failure handling & structured error responses
