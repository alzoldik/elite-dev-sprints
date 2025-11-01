Sure — here’s your full **Markdown (.md)** version of the backend documentation for the **Flutter mobile login with OTP flow** 👇

---

```markdown
# 🔐 Login with Mobile Number + OTP (Backend API)

This document describes the backend API used by the **Flutter mobile app** for user authentication using **mobile number + OTP verification**.

---

## 1️⃣ Send OTP

### **Endpoint**
```

POST /api/auth/send-otp

````

### **Description**
Send an OTP code via SMS to a user’s phone for login or registration.

### **Request**
```json
{
  "country_code": "+20",
  "mobile": "1012345678",
  "purpose": "login"
}
````

### **Response (Success)**

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

### **Response (Error)**

```json
{
  "status": false,
  "message": "Too many attempts. Try again later."
}
```

---

## 2️⃣ Verify OTP

### **Endpoint**

```
POST /api/auth/verify-otp
```

### **Description**

Verify the OTP code entered by the user.
If verification succeeds, the backend returns full user data and JWT tokens.

### **Request**

```json
{
  "verification_id": "verif_abc123",
  "otp_code": "123456",
  "country_code": "+20",
  "mobile": "1012345678",
  "fcm_token": "abcd1234xyz_fcm_token_here"
}
```

### **Response (Success)**

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

### **Response (Error)**

```json
{
  "status": false,
  "message": "Invalid or expired OTP"
}
```

---

## 3️⃣ Resend OTP

### **Endpoint**

```
POST /api/auth/resend-otp
```

### **Request**

```json
{
  "verification_id": "verif_abc123"
}
```

### **Response**

```json
{
  "status": true,
  "message": "OTP resent",
  "data": {
    "expires_in": 300
  }
}
```

---

## 4️⃣ Authenticated Requests

All authenticated requests must include the JWT token:

```
Authorization: Bearer <jwt_token>
```

---

## ⚙️ Security & Implementation Notes

* **OTP length:** 4–6 digits (random and unique).
* **OTP expiry:** 3–10 minutes (`expires_in` indicates actual expiry time).
* **Rate limiting:**

  * Limit OTP sends per phone/IP.
  * Allow resending only after 60 seconds.
  * Max 3 attempts per verification ID.
* **Storage:** Store OTPs hashed and linked to `verification_id`.
* **Cleanup:** Invalidate OTP after successful verification.
* **Encryption:** Always use HTTPS.
* **Token storage:** Keep JWTs in secure local storage (`flutter_secure_storage`).
* **Push notifications:** Update the user’s `fcm_token` on each successful verification.

---

## 📱 Flutter Integration Tips

1. **Send OTP:**
   Call `/api/auth/send-otp`, store `verification_id`.

2. **Show OTP UI:**
   Let the user input the code; show countdown timer using `expires_in`.

3. **Verify OTP:**
   Call `/api/auth/verify-otp` with code, number, and `fcm_token`.

4. **Save JWT:**
   Store `jwt_token` securely for future requests.

5. **Authorized Requests:**
   Always include the `Authorization` header:

   ```
   Authorization: Bearer <jwt_token>
   ```

---

## ✅ Example Flow Diagram (Simplified)

```
User enters phone number
       ↓
[POST] /send-otp
       ↓
SMS OTP sent
       ↓
User enters OTP
       ↓
[POST] /verify-otp
       ↓
Server verifies OTP → issues JWT + user data
       ↓
App stores JWT securely → user logged in
```

---

*Document version: 1.0 (Updated: 2025-11-01)*

```

---

Would you like me to generate this as a **downloadable `.md` file** (e.g., `login_api_doc.md`)?
```
