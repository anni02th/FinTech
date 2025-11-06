# FinTrustChain Server - Deployment Guide

## 🚀 Production-Ready Features

- ✅ **Security**: Helmet.js, CORS, rate limiting
- ✅ **Logging**: Winston logger with file rotation
- ✅ **Compression**: Response compression for better performance
- ✅ **Error Handling**: Global error handler with proper logging
- ✅ **Health Check**: `/health` endpoint for monitoring
- ✅ **Docker Support**: Containerized with Docker & docker-compose
- ✅ **Rate Limiting**: Protection against brute force attacks

## 📋 Prerequisites

- Node.js 20+
- MongoDB Atlas account or local MongoDB
- Docker & Docker Compose (for containerized deployment)

## 🔧 Setup Instructions

### 1. Environment Variables

Copy `.env.example` to `.env` and update with your values:

```bash
cp .env.example .env
```

**Important variables to update:**

- `MONGO_URI`: Your MongoDB connection string
- `JWT_SECRET`: Strong secret key (min 32 characters)
- `EMAIL_PASSWORD`: SendGrid API key or Gmail app password
- `FRONTEND_URL`: Your frontend domain
- `PHONEPE_*`: PhonePe credentials

### 2. Install Dependencies

```bash
npm install
```

### 3. Run Development Server

```bash
npm run dev
```

## 🐳 Docker Deployment

### Build and Run with Docker Compose

```bash
# Build the image
npm run docker:build

# Start the container
npm run docker:run

# View logs
npm run docker:logs

# Stop the container
npm run docker:stop
```

### Manual Docker Commands

```bash
# Build image
docker build -t fintrustchain-server .

# Run container
docker run -d -p 3000:3000 --env-file .env --name fintrustchain fintrustchain-server

# View logs
docker logs -f fintrustchain

# Stop container
docker stop fintrustchain
```

## 🌐 Cloud Deployment Options

### Railway (Recommended)

1. Push code to GitHub
2. Connect Railway to your repository
3. Add environment variables in Railway dashboard
4. Deploy automatically

### Render

1. Create new Web Service
2. Connect GitHub repository
3. Set build command: `npm install`
4. Set start command: `npm start`
5. Add environment variables

### AWS ECS/Fargate

1. Push Docker image to ECR
2. Create ECS task definition
3. Deploy to Fargate
4. Configure load balancer

### DigitalOcean App Platform

1. Connect GitHub repository
2. Detect Dockerfile automatically
3. Configure environment variables
4. Deploy

## 📊 Monitoring & Logging

### Health Check

```bash
curl http://localhost:3000/health
```

Response:

```json
{
  "status": "ok",
  "timestamp": "2025-10-24T12:00:00.000Z",
  "uptime": 1234.56,
  "environment": "production"
}
```

### View Logs

```bash
# Application logs
npm run logs

# Docker logs
npm run docker:logs

# Error logs only
tail -f logs/error.log
```

## 🔒 Security Checklist

- [x] Helmet.js enabled for security headers
- [x] CORS configured with whitelist
- [x] Rate limiting on auth routes (5 req/15min)
- [x] Rate limiting on API routes (100 req/15min)
- [x] JWT with strong secret
- [x] Password hashing with bcrypt
- [x] File upload size limits (5MB)
- [x] Environment variables not committed
- [x] Non-root Docker user
- [x] Input validation on all routes

## 📝 API Endpoints

- `GET /health` - Health check
- `POST /api/v1/auth/register` - User registration
- `POST /api/v1/auth/login` - User login
- `GET /api/v1/auth/verify-email/:token` - Email verification
- And more... (see routes folder)

## 🛠️ Troubleshooting

### Email not sending

- Verify SendGrid API key is active
- Check sender email is verified
- Review logs: `tail -f logs/error.log`

### MongoDB connection failed

- Verify MongoDB URI is correct
- Check network access in MongoDB Atlas
- Ensure IP whitelist includes your server

### Docker container not starting

- Check environment variables: `docker-compose config`
- View container logs: `docker logs fintrustchain-server`
- Verify port 3000 is not in use

## 📧 Support

For issues, please create a GitHub issue or contact the development team.

## 📜 License

This project is part of a portfolio/resume project.
