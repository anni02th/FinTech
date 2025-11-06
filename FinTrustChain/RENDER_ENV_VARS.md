# 🔐 Environment Variables for Render Dashboard

## Instructions

1. Go to Render Dashboard: https://dashboard.render.com
2. Click on "fintrustchain-server"
3. Go to "Environment" tab
4. Click "Add Environment Variable"
5. Copy-paste these values (one by one):

---

## Required Environment Variables

### MongoDB Configuration

```
MONGO_URI=mongodb+srv://piyushnashikkar2004:dg0JjumfEmhMF8sA@cluster0.r0g7su6.mongodb.net/fintrustchain?retryWrites=true&w=majority&appName=Cluster0

MONGO_PASSWORD=dg0JjumfEmhMF8sA
```

### JWT Configuration

⚠️ **IMPORTANT: Change JWT_SECRET to a strong random value!**

```
JWT_SECRET=production_secret_please_change_this_to_very_secure_random_string_minimum_32_characters_long

JWT_EXPIRES_IN=90d
```

### Email Configuration (Gmail)

```
EMAIL_HOST=smtp.gmail.com

EMAIL_PORT=587

EMAIL_USERNAME=piyushnas74@gmail.com

EMAIL_PASSWORD=zvcemiitvtuvapdk

EMAIL_FROM=FinTrustChain <piyushnas74@gmail.com>
```

### Frontend Configuration

⚠️ **Update this after deploying your frontend**

```
FRONTEND_URL=http://localhost:5173
```

### PhonePe Configuration (Sandbox)

```
PHONEPE_CLIENT_ID=TEST-M235FNVAAUNJ4_25081

PHONEPE_CLIENT_SECRET=YWIzNmM1MmMtZDgwZS00NjI4LTg5ZmEtODJjZTBmZDEyMzE1

PHONEPE_CLIENT_VERSION=1

PHONEPE_HOST_URL=https://api-preprod.phonepe.com/apis/pg-sandbox
```

### PhonePe Webhook Configuration

```
PHONEPE_WEBHOOK_USERNAME=FinTrustChainUser

PHONEPE_WEBHOOK_PASSWORD=SuperSecretPassword123
```

### File Upload Configuration

```
MAX_FILE_SIZE=5242880
```

---

## After Adding Variables

1. Click **"Save Changes"**
2. Render will **automatically redeploy** (takes 2-3 minutes)
3. Watch the logs for successful deployment
4. Test your API: https://fintrustchain-server.onrender.com/health

---

## Security Checklist

- [ ] Change JWT_SECRET to a secure random string (min 32 chars)
- [ ] Update FRONTEND_URL after deploying frontend
- [ ] Verify MongoDB Atlas allows connections from 0.0.0.0/0
- [ ] Test all API endpoints after redeployment
- [ ] Monitor logs for any errors

---

## Generate Secure JWT_SECRET

Use one of these methods:

### Option 1: Node.js

```bash
node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
```

### Option 2: OpenSSL

```bash
openssl rand -hex 32
```

### Option 3: Online Generator

Visit: https://randomkeygen.com/
Choose "CodeIgniter Encryption Keys" (256-bit)

---

## Test After Deployment

```bash
# Health Check
curl https://fintrustchain-server.onrender.com/health

# Expected: {"status":"ok","uptime":...}
```

---

## Your API is live at:

```
https://fintrustchain-server.onrender.com
```

🎉 **Congratulations! Your backend is production-ready!**
