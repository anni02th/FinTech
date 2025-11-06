# 🚀 Render Deployment Guide for FinTrustChain

## Step-by-Step Deployment Instructions

### 1. Create Render Account

- Go to https://render.com
- Click **"Get Started for Free"**
- Sign up with GitHub (recommended)

### 2. Connect Your GitHub Repository

1. **Click "New +"** in Render dashboard
2. Select **"Web Service"**
3. Click **"Build and deploy from a Git repository"**
4. Click **"Connect account"** (if not connected)
5. Find **"FinTrustChain"** repository
6. Click **"Connect"**

### 3. Configure Your Web Service

#### Basic Settings:

- **Name**: `fintrustchain-server` (or your choice)
- **Region**: Choose closest to your users (e.g., Oregon, Frankfurt)
- **Branch**: `main`
- **Root Directory**: `server` (IMPORTANT!)
- **Runtime**: Render will auto-detect **Docker** ✅

#### Build Settings (Auto-detected from Dockerfile):

- **Build Command**: Docker will handle this
- **Start Command**: Docker will handle this
- Render will use your `Dockerfile` automatically

### 4. Add Environment Variables

Click **"Advanced"** → **"Add Environment Variable"** and add these:

#### Required Variables:

```env
# MongoDB (Use your MongoDB Atlas connection)
MONGO_URI=mongodb+srv://piyushnashikkar2004:dg0JjumfEmhMF8sA@cluster0.r0g7su6.mongodb.net/fintrustchain?retryWrites=true&w=majority&appName=Cluster0

# JWT Configuration (CHANGE THIS IN PRODUCTION!)
JWT_SECRET=your_super_secure_secret_key_min_32_characters_long_production
JWT_EXPIRES_IN=90d

# Server Configuration
PORT=3000
NODE_ENV=production

# Email Configuration (Gmail)
EMAIL_HOST=smtp.gmail.com
EMAIL_PORT=587
EMAIL_USERNAME=piyushnas74@gmail.com
EMAIL_PASSWORD=zvcemiitvtuvapdk
EMAIL_FROM=FinTrustChain <piyushnas74@gmail.com>

# Frontend URL (Update after deploying frontend)
FRONTEND_URL=https://your-frontend-domain.onrender.com

# PhonePe Sandbox
PHONEPE_CLIENT_ID=TEST-M235FNVAAUNJ4_25081
PHONEPE_CLIENT_SECRET=YWIzNmM1MmMtZDgwZS00NjI4LTg5ZmEtODJjZTBmZDEyMzE1
PHONEPE_CLIENT_VERSION=1
PHONEPE_HOST_URL=https://api-preprod.phonepe.com/apis/pg-sandbox

# PhonePe Webhook
PHONEPE_WEBHOOK_USERNAME=FinTrustChainUser
PHONEPE_WEBHOOK_PASSWORD=SuperSecretPassword123

# File Upload
MAX_FILE_SIZE=5242880
```

**Important Notes:**

- ⚠️ **Change `JWT_SECRET`** to a strong, unique value for production
- ⚠️ Update `FRONTEND_URL` after deploying your frontend
- ✅ No spaces around `=` signs
- ✅ No quotes needed for values in Render

### 5. Configure Health Check (Optional but Recommended)

In **"Advanced"** settings:

- **Health Check Path**: `/health`
- Render will monitor this endpoint

### 6. Deploy! 🚀

1. Click **"Create Web Service"**
2. Render will:
   - Build your Docker image (~3-5 minutes)
   - Deploy the container
   - Assign you a URL: `https://fintrustchain-server.onrender.com`

### 7. Monitor Deployment

Watch the deployment logs in real-time:

- You'll see Docker build progress
- Server startup logs
- MongoDB connection status

**Expected logs:**

```
Building Docker image...
[+] Building 120.5s
Successfully built image
Deploying...
Server running on port 3000
🟢 MongoDB Connected
```

### 8. Test Your Deployment

Once deployed, test these endpoints:

```bash
# Health Check
curl https://fintrustchain-server.onrender.com/health

# Expected Response:
{
  "status": "ok",
  "timestamp": "2025-10-24T12:00:00.000Z",
  "uptime": 123.45,
  "environment": "production"
}

# Root Endpoint
curl https://fintrustchain-server.onrender.com/

# Expected Response:
Welcome to FinTrustChain API
```

### 9. Update MongoDB Atlas Whitelist

1. Go to MongoDB Atlas → Network Access
2. Click **"Add IP Address"**
3. Add **`0.0.0.0/0`** (Allow from anywhere)
   - Or add Render's specific IPs for better security
4. Click **"Confirm"**

### 10. Update Frontend CORS

Once you have your Render URL, update your frontend to use:

```javascript
const API_URL = "https://fintrustchain-server.onrender.com/api/v1";
```

---

## 🎯 Post-Deployment Configuration

### Update Environment Variables

If you need to change any environment variables:

1. Go to Render Dashboard
2. Click on your service
3. Go to **"Environment"** tab
4. Update variables
5. Click **"Save Changes"**
6. Render will automatically redeploy

### View Logs

To debug issues:

1. Click on your service
2. Go to **"Logs"** tab
3. View real-time logs

### Custom Domain (Optional)

To use your own domain:

1. Go to **"Settings"** tab
2. Click **"Add Custom Domain"**
3. Enter your domain: `api.fintrustchain.com`
4. Add DNS records as instructed
5. Render will provide free SSL certificate

---

## 🔒 Security Checklist

Before going live:

- [ ] Change `JWT_SECRET` to a strong, unique value
- [ ] Update `FRONTEND_URL` with your deployed frontend URL
- [ ] Verify MongoDB Atlas IP whitelist
- [ ] Test all API endpoints
- [ ] Enable SendGrid for production emails (optional)
- [ ] Set up monitoring alerts in Render
- [ ] Review rate limiting settings
- [ ] Test file uploads work correctly
- [ ] Verify PhonePe integration

---

## 💰 Render Free Tier Limits

**What you get for free:**

- ✅ 750 hours/month (enough for 1 service 24/7)
- ✅ Automatic SSL certificates
- ✅ Automatic deploys from GitHub
- ✅ 512 MB RAM
- ✅ 0.1 CPU
- ⚠️ **Spins down after 15 minutes of inactivity**
- ⚠️ **Cold start: 30-60 seconds on first request**

**To prevent cold starts:**

- Upgrade to paid plan ($7/month)
- Or use a ping service (uptime monitor)

---

## 🐛 Troubleshooting

### Build Fails

- Check Dockerfile syntax
- Ensure all dependencies in package.json
- Review build logs in Render

### Server Won't Start

- Check environment variables (no typos)
- Verify MongoDB connection string
- Review server logs

### 404 Errors

- Ensure Root Directory is set to `server`
- Check route paths in your code
- Verify PORT is 3000

### MongoDB Connection Failed

- Whitelist 0.0.0.0/0 in MongoDB Atlas
- Check MONGO_URI is correct
- Verify network access settings

### Emails Not Sending

- For Gmail: Use App Password (not regular password)
- For SendGrid: Verify API key is active
- Check email logs in Render

---

## 🎉 Success!

Once deployed, your API will be available at:

```
https://fintrustchain-server.onrender.com
```

Add this URL to your frontend and you're live! 🚀

---

## 📊 Next Steps

1. Deploy your frontend to Render/Vercel/Netlify
2. Update `FRONTEND_URL` in Render environment variables
3. Test full application flow
4. Set up monitoring (UptimeRobot, Better Uptime)
5. Add to your resume with live demo link! 🎯

**Your FinTrustChain server is now production-ready and deployed!** 🎉
