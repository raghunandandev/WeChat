# Deployment Guide: WeChat on Render

This guide provides step-by-step instructions to deploy the WeChat application to Render.

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Backend Deployment](#backend-deployment)
3. [Frontend Deployment](#frontend-deployment)
4. [Environment Configuration](#environment-configuration)
5. [Post-Deployment Verification](#post-deployment-verification)

---

## Prerequisites

Before starting, ensure you have:
- GitHub account with the WeChat repository pushed
- Render account (sign up at [render.com](https://render.com))
- MongoDB Atlas account with a cluster created
- Git installed locally

---

## Backend Deployment

### Step 1: Prepare Backend for Render

1. **Update `app.js` to use environment variables:**

```javascript
// backend/src/app.js
const PORT = process.env.PORT || 8000;
const MONGODB_URI = process.env.MONGODB_URI || "mongodb+srv://username:password@cluster.mongodb.net/";

app.set("port", PORT);

const start = async () => {
    try {
        const connectionDb = await mongoose.connect(MONGODB_URI);
        console.log(`MONGO Connected DB Host: ${connectionDb.connection.host}`);
        
        server.listen(app.get("port"), () => {
            console.log(`Listening on port ${app.get("port")}`);
        });
    } catch (error) {
        console.error("Database connection failed:", error);
        process.exit(1);
    }
};

start();
```

2. **Create `.env.example` file:**

```bash
# backend/.env.example
PORT=8000
MONGODB_URI=mongodb+srv://username:password@cluster.mongodb.net/wechat
NODE_ENV=production
```

3. **Ensure `.gitignore` has `.env` listed:**

```bash
# backend/.gitignore
.env
node_modules/
*.log
.DS_Store
```

### Step 2: Create Render Web Service for Backend

1. Go to [Render Dashboard](https://dashboard.render.com)
2. Click **New +** → **Web Service**
3. Select **Connect a repository** or **Use existing repository**
4. Choose your WeChat repository
5. Configure the service:
   - **Name:** `wechat-backend`
   - **Environment:** `Node`
   - **Region:** Choose closest to your location
   - **Branch:** `main` (or your default branch)
   - **Build Command:** `npm install`
   - **Start Command:** `npm start`
   - **Plan:** Free or Paid (Free tier has limitations)

### Step 3: Add Environment Variables

In the Render dashboard for the backend service:

1. Go to **Environment** tab
2. Add the following environment variables:
   - **MONGODB_URI:** Your MongoDB Atlas connection string
   - **NODE_ENV:** `production`
   - **PORT:** `8000` (Render sets this automatically)

Example MongoDB URI format:
```
mongodb+srv://username:password@cluster0.xxxxx.mongodb.net/wechat?retryWrites=true&w=majority
```

### Step 4: Get Backend URL

After deployment:
1. Wait for the build to complete
2. Copy the web service URL (e.g., `https://wechat-backend.onrender.com`)
3. This URL will be used for frontend configuration

---

## Frontend Deployment

### Step 1: Prepare Frontend for Render

1. **Update `environment.js` with backend URL:**

```javascript
// frontend/src/environment.js
const BACKEND_URL = process.env.REACT_APP_BACKEND_URL || "http://localhost:8000";

export { BACKEND_URL };
```

2. **Create `.env.example` for frontend:**

```bash
# frontend/.env.example
REACT_APP_BACKEND_URL=https://wechat-backend.onrender.com
```

3. **Update `package.json` with build command:**

```json
{
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  }
}
```

### Step 2: Create Render Static Site for Frontend

1. Go to [Render Dashboard](https://dashboard.render.com)
2. Click **New +** → **Static Site**
3. Select your WeChat repository
4. Configure:
   - **Name:** `wechat-frontend`
   - **Environment:** `Node`
   - **Region:** Same as backend (for low latency)
   - **Branch:** `main`
   - **Build Command:** `cd frontend && npm install && npm run build`
   - **Publish Directory:** `frontend/build`

### Step 3: Add Environment Variables

In the frontend service environment variables:

1. **REACT_APP_BACKEND_URL:** Use the backend URL from Step 1
   - Example: `https://wechat-backend.onrender.com`

### Step 4: Deploy Frontend

1. Click **Create Static Site**
2. Wait for the build and deployment to complete
3. Your frontend will be available at a URL like: `https://wechat-frontend.onrender.com`

---

## Environment Configuration

### MongoDB Atlas Setup

1. **Get Connection String:**
   - Go to MongoDB Atlas dashboard
   - Click **Connect** on your cluster
   - Choose **Connect your application**
   - Copy the connection string
   - Replace `<username>` and `<password>` with your credentials

2. **Example Connection String:**
```
mongodb+srv://admin:mypassword@cluster0.abc123.mongodb.net/wechat?retryWrites=true&w=majority
```

3. **Add IP Whitelist:**
   - In MongoDB Atlas, go to **Network Access**
   - Add `0.0.0.0/0` to allow Render to connect (not recommended for production)
   - Better: Use Render's IP range if available

### CORS Configuration (Optional)

Update backend `app.js` if needed:

```javascript
app.use(cors({
    origin: "https://wechat-frontend.onrender.com",
    credentials: true,
    methods: ["GET", "POST", "PUT", "DELETE"],
    allowedHeaders: ["Content-Type", "Authorization"]
}));
```

---

## Post-Deployment Verification

### Step 1: Verify Backend Health

```bash
# Test backend API
curl https://wechat-backend.onrender.com/api/v1/users

# Should return a response (may be 404 if no route, but server is running)
```

### Step 2: Check Frontend Loading

1. Visit `https://wechat-frontend.onrender.com`
2. Should load the landing page without errors
3. Check browser console for any error messages

### Step 3: Test Basic Functionality

1. **Registration:**
   - Navigate to `/auth`
   - Create a new user account
   - Should successfully register

2. **Database Connection:**
   - Check Render backend logs for database connection messages
   - Look for: "MONGO Connected DB Host: ..."

3. **WebSocket Connection:**
   - Try creating/joining a meeting
   - Check browser console for Socket.io connection status

### Step 4: Monitor Logs

**Backend Logs:**
- In Render dashboard, click the backend service
- Go to **Logs** tab to view real-time logs

**Frontend Logs:**
- Go to **Logs** tab in the frontend service

---

## Troubleshooting

### Common Issues

**Issue: Backend deployment fails**
- Check build command: `npm install && npm start`
- Verify `package.json` has all dependencies
- Check logs for specific error messages

**Issue: MongoDB connection error**
- Verify `MONGODB_URI` environment variable is set correctly
- Check MongoDB Atlas IP whitelist includes Render servers
- Ensure database name in URI is correct

**Issue: Frontend can't reach backend**
- Verify `REACT_APP_BACKEND_URL` environment variable is set
- Check frontend logs for CORS errors
- Ensure backend is running and accessible

**Issue: Socket.io connection fails**
- Add backend URL to Socket.io client configuration
- Check CORS settings in backend
- Verify WebSocket support is enabled

### Enable Live Logs

```bash
# Frontend - add logging
console.log("Backend URL:", process.env.REACT_APP_BACKEND_URL);
```

### Update Deployment

To update either service:
1. Push changes to GitHub
2. Render automatically redeploys on push (if auto-deploy is enabled)
3. Or manually trigger rebuild from Render dashboard

---

## Production Recommendations

1. **Database:**
   - Use a paid MongoDB Atlas cluster for production
   - Enable automatic backups
   - Restrict IP whitelist to Render services only

2. **Backend:**
   - Use paid Render plan for better performance
   - Add error monitoring (e.g., Sentry)
   - Implement rate limiting
   - Add request logging

3. **Frontend:**
   - Use Render's premium static site plan
   - Add CDN caching
   - Enable gzip compression
   - Implement error tracking (e.g., Sentry)

4. **Security:**
   - Never commit `.env` files
   - Use strong database passwords
   - Enable HTTPS (Render provides this by default)
   - Implement input validation and sanitization

5. **Monitoring:**
   - Set up health checks
   - Monitor error logs regularly
   - Use uptime monitoring services
   - Set up alerts for deployment failures

---

## Useful Links

- [Render Documentation](https://render.com/docs)
- [Render Web Services](https://render.com/docs/web-services)
- [Render Static Sites](https://render.com/docs/static-sites)
- [MongoDB Atlas](https://www.mongodb.com/cloud/atlas)
- [Socket.io Deployment Guide](https://socket.io/docs/v4/socket-io-on-aws/)

---

## Support

For issues with Render deployment:
- Check Render status page
- Review Render documentation
- Contact Render support via dashboard

For MongoDB issues:
- Review MongoDB Atlas documentation
- Check connection string format
- Verify IP whitelist settings
