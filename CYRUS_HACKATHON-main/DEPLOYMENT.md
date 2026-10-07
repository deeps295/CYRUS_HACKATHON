# 🚀 Deployment Guide for OCCUPRA

The OCCUPRA application is designed to be easily deployable. We recommend using **Render** for the full-stack backend (Node.js + SQLite) and **Vercel** for the React frontend.

---

## 1. Deploying the Backend (Render.com)

1. **Create an account** on [Render](https://render.com/).
2. Click **New +** and select **Web Service**.
3. Connect your GitHub repository containing this code.
4. **Configure the Web Service:**
   - **Name:** `occupra-backend`
   - **Root Directory:** `backend` *(Important!)*
   - **Environment:** `Node`
   - **Build Command:** `npm install && npm run build && npx prisma generate && npx prisma db push && npm run prisma:seed`
   - **Start Command:** `npm start`
5. **Environment Variables:**
   - Add `DATABASE_URL` (Optional if using SQLite which creates a file automatically, but Render ephemeral disks lose data on restart unless you attach a **Render Disk**. For hackathons, the in-memory/ephemeral SQLite is fine as the seed script populates data on build).
   - Add `PORT` = `5000`
   - Add `JWT_SECRET` = `your_super_secret_key_123`
6. Click **Create Web Service**. Wait for the build to finish.
7. Once deployed, copy your backend URL (e.g., `https://occupra-backend.onrender.com`).

---

## 2. Deploying the Frontend (Vercel)

1. **Create an account** on [Vercel](https://vercel.com/).
2. Click **Add New... > Project** and import your GitHub repository.
3. **Configure the Project:**
   - **Project Name:** `occupra-app`
   - **Framework Preset:** `Vite`
   - **Root Directory:** `frontend` *(Important!)*
4. **Set Environment Variables:**
   - To make the frontend communicate with the Render backend, you need to expose the API URL.
   - You need to add a `.env.production` file in your frontend or update `api.ts` to use your production URL.
   - *Since this is a quick deployment:* Go to `frontend/src/services/api.ts` and change:
     ```typescript
     const BASE_URL = '/api';
     ```
     to:
     ```typescript
     const BASE_URL = import.meta.env.PROD ? 'https://occupra-backend.onrender.com/api' : '/api';
     ```
     *(Make sure to replace the URL with your actual Render URL)*.
5. Click **Deploy**. Vercel will automatically build (`npm run build`) and serve your React app.
6. Once finished, Vercel will provide you with a live domain (e.g., `https://occupra.vercel.app`).

---

## 3. Post-Deployment Checklist

- Open your Vercel URL.
- Test the login with the demo accounts:
  - **Student:** `student@campus.ai` / `student123`
  - **Admin:** `admin@campus.ai` / `admin123`
- The IoT stream (SSE) should connect successfully to the Render backend, turning the indicator green.
- If the heatmap and map load correctly with live data, your deployment is a success!

*(Note: If you run into CORS issues, ensure your Render backend has the CORS middleware enabled for your Vercel domain in `backend/src/index.ts`.)*
