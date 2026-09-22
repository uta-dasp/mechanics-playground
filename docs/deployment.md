# Deployment Guide

This document describes how to configure, build, and deploy the Interactive Mechanics Playground for production.

---

## 1. Environment Configuration

### Frontend Configuration
The frontend uses Vite environment variables:
- `VITE_API_URL`: The absolute or relative base URL for the backend API.
  - Development default: `http://localhost:8000`
  - Production example: `https://api.mechanics.example.com` or `/api` (when reverse-proxied)

Create a `.env.production` file in `frontend/`:
```bash
VITE_API_URL=https://api.yourdomain.com
```

### Backend Configuration
The FastAPI backend supports environment variables:
- `PORT`: HTTP port to bind (default: `8000`).
- `HOST`: Host interface to bind (default: `0.0.0.0`).
- `CORS_ORIGINS`: Comma-separated list of allowed origins.
  - Default: `http://localhost:5173,http://localhost:3000`
  - Production example: `https://yourdomain.com,https://ai-playground.yourdomain.com`

---

## 2. Production Build

### Frontend
```bash
cd frontend
npm install
npm run build
```
The output artifacts are emitted to `frontend/dist/`. These static assets can be served by Nginx, Cloudflare Pages, Vercel, AWS S3/CloudFront, or any static host.

### Backend
Run with an ASGI production server like `uvicorn` or `gunicorn`:
```bash
cd backend
pip install -r requirements.txt
uvicorn app.main:app --host 0.0.0.0 --port 8000 --workers 4
```

---

## 3. Containerization (Docker)

### Backend `Dockerfile`
```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY app ./app
EXPOSE 8000
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### Reverse Proxy Configuration (Nginx snippet)
```nginx
server {
    listen 80;
    server_name mechanics.example.com;

    location / {
        root /usr/share/nginx/html;
        try_files $uri $uri/ /index.html;
    }

    location /api/ {
        proxy_pass http://backend:8000/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```
