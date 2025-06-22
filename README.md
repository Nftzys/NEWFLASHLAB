# NEWFLASHLAB

This repository contains a FastAPI backend and a Next.js frontend used for a photo event application.

## Requirements

- Node.js 18+
- Python 3.10+
- nginx (used as a reverse proxy)

Python packages are listed in `requirements.txt` and can be installed with:

```bash
pip install -r requirements.txt
```

Install frontend dependencies with:

```bash
cd frontend
npm install
```

## Building

1. **Backend**
   ```bash
   uvicorn face_match_app.main:app --host 0.0.0.0 --port 8000
   ```
   Use a process manager such as systemd or supervisor for production.

2. **Frontend**
   ```bash
   cd frontend
   npm run build
   npm start
   ```
   The start script listens on port **3020**.

## nginx configuration

Example reverse proxy configuration (see `deploy/nginx.conf`):

```nginx
server {
    listen 80;
    server_name example.com;

    location /api/ {
        proxy_pass http://127.0.0.1:8000/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location / {
        proxy_pass http://127.0.0.1:3020;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Point your domain to the VPS and enable this server block. Requests to `/api` will reach the FastAPI app while other paths will be served by the Next.js frontend.

## Environment variables

The frontend reads `NEXT_PUBLIC_API_URL` to reach the backend. When using the above nginx config, set it to `/api` before building:

```bash
export NEXT_PUBLIC_API_URL=/api
npm run build
```

## Running with Docker Compose

An example setup can be created using docker-compose if desired.
