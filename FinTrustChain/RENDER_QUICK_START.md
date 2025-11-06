# 🎯 Render Deployment - Quick Reference

## Environment Variables to Add in Render

Copy these to Render Dashboard → Environment Variables:

```env
MONGO_URI=mongodb+srv://piyushnashikkar2004:dg0JjumfEmhMF8sA@cluster0.r0g7su6.mongodb.net/fintrustchain?retryWrites=true&w=majority&appName=Cluster0
JWT_SECRET=change_this_to_a_very_strong_secret_key_minimum_32_characters_long
JWT_EXPIRES_IN=90d
PORT=3000
NODE_ENV=production
EMAIL_HOST=smtp.gmail.com
EMAIL_PORT=587
EMAIL_USERNAME=piyushnas74@gmail.com
EMAIL_PASSWORD=zvcemiitvtuvapdk
EMAIL_FROM=FinTrustChain <piyushnas74@gmail.com>
FRONTEND_URL=https://your-frontend-url.onrender.com
PHONEPE_CLIENT_ID=TEST-M235FNVAAUNJ4_25081
PHONEPE_CLIENT_SECRET=YWIzNmM1MmMtZDgwZS00NjI4LTg5ZmEtODJjZTBmZDEyMzE1
PHONEPE_CLIENT_VERSION=1
PHONEPE_HOST_URL=https://api-preprod.phonepe.com/apis/pg-sandbox
PHONEPE_WEBHOOK_USERNAME=FinTrustChainUser
PHONEPE_WEBHOOK_PASSWORD=SuperSecretPassword123
MAX_FILE_SIZE=5242880
```

## Render Settings

- **Root Directory**: `server`
- **Runtime**: Docker (auto-detected)
- **Health Check Path**: `/health`
- **Region**: Oregon (or closest to you)
- **Plan**: Free

## Post-Deployment Checklist

- [ ] Test health endpoint: `https://your-app.onrender.com/health`
- [ ] Whitelist 0.0.0.0/0 in MongoDB Atlas
- [ ] Update FRONTEND_URL after deploying frontend
- [ ] Change JWT_SECRET to a strong value
- [ ] Test API endpoints work
- [ ] Monitor logs for errors

## Your API will be available at:

```
https://fintrustchain-server.onrender.com
```

(Replace with your actual Render URL)
