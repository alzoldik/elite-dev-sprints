# 🔐 Login with Mobile Number + OTP API Documentation

This document describes the backend endpoints used by the Flutter mobile app for authentication using **mobile number with OTP verification**.

---

## 1️⃣ Send OTP

**Endpoint**
```
POST /api/auth/send-otp
```

### Description
Sends a one-time password (OTP) to the user's mobile number for login or registration.

### Request (JSON)
```json
{
  "country_code": "+20",
  "mobile": "1012345678",
  "purpose": "login"
}
```

| Field | Type | Required | Description |
|-------|------|-----------|-------------|
| `country_code` | string | ✅ | User’s country code (e.g. +20 for Egypt) |
| `mobile` | string | ✅ | User’s mobile number without country code |
| `purpose` | string | optional | `"login"` or `"register"` |

### Success Response
```json
{
  "status": true,
  "message": "OTP sent",
  "data": {
    "verification_id": "verif_abc123",
    "expires_in": 300
  }
}
```

### Failure Response
```json
{
  "status": false,
  "message": "Too many attempts. Try again later."
}
```

---

## 2️⃣ Verify OTP

**Endpoint**
```
POST /api/auth/verify-otp
```

### Description
Verifies the OTP code.  
If valid, logs in the user (and optionally creates an account if it doesn't exist).

### Request (JSON)
```json
{
  "verification_id": "verif_abc123",
  "otp_code": "123456",
  "country_code": "+20",
  "mobile": "1012345678",
  "fcm_token": "abcd1234xyz_fcm_token_here"
}
```

| Field | Type | Required | Description |
|-------|------|-----------|-------------|
| `verification_id` | string | ✅ | The ID returned from `/send-otp` |
| `otp_code` | string | ✅ | The OTP entered by the user |
| `country_code` | string | ✅ | The same code used in send-otp |
| `mobile` | string | ✅ | User’s phone number |
| `fcm_token` | string | ✅ | Firebase Cloud Messaging token for notifications |

### Success Response
```json
{
  "status": true,
  "message": "Verification successful",
  "data": {
    "user": {
      "id": 12,
      "full_name": "Aly Mohamed",
      "phone": "+201012345678",
      "profile_image": "https://cdn.example.com/uploads/users/12/profile.jpg",
      "email": "aly@example.com",
      "role": "student",
      "fcm_token": "abcd1234xyz_fcm_token_here",
      "created_at": "2025-11-01T22:30:00Z"
    },
    "jwt_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refresh_token": "refresh_XXXXX",
    "token_expires_in": 3600
  }
}
```

### Failure Response
```json
{
  "status": false,
  "message": "Invalid or expired OTP"
}
```

---

## 3️⃣ Resend OTP

**Endpoint**
```
POST /api/auth/resend-otp
```

### Request
```json
{
  "verification_id": "verif_abc123"
}
```

### Success Response
```json
{
  "status": true,
  "message": "OTP resent",
  "data": { "expires_in": 300 }
}
```

---

## 4️⃣ Authenticated Requests

All authenticated API calls must include the JWT token in the header:

```
Authorization: Bearer <jwt_token>
```

---

## 🛡️ Security & Implementation Notes

- OTP length: **4–6 digits** (avoid predictable patterns).  
- OTP expiry: **3–10 minutes** recommended.  
- Rate limiting: **max 3 attempts per OTP**, **cooldown 60s** for resend.  
- Store **hashed OTP** with short TTL (never plain text).  
- Invalidate `verification_id` after successful verification.  
- Use **HTTPS** and store `jwt_token` securely on mobile (e.g., `flutter_secure_storage`).  
- Log and monitor suspicious OTP or resend activity.

---

## 📱 Flutter Integration Guide

1. **Flow**
   - `send-otp` → show OTP UI → `verify-otp`
   - On success, store `jwt_token` and `user` locally

2. **Storage**
   - Use `flutter_secure_storage` for sensitive tokens
   - Keep `user` info in local cache or provider state

3. **FCM Token**
   - Always send latest `fcm_token` during login
   - Or use `/api/user/update-fcm` if refreshed later

4. **UI Tip**
   - Use the `expires_in` field to show OTP countdown

---

## ⚙️ Example Headers

```
Content-Type: application/json
Accept: application/json
Authorization: Bearer <jwt_token>
```

---

**Author:** Backend API Spec for Flutter Login  
**Last Updated:** 2025-11-02  
**Version:** 1.0.0
