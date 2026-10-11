# 🥗 MealConnect - Turning Excess into Access

## About
**Meal Connect** is a community-driven platform that bridges the gap between surplus and need — connecting restaurants, NGOs, and individuals to reduce food waste and fight hunger.

## Mission
Our mission is to create a world where no meal is wasted and no person goes hungry by transforming surplus food into shared hope through smart, seamless connections.

## For Restaurants (Donors)
Restaurants and food providers can easily list surplus food through their dashboard.  
Each donation includes dish details, pickup location, and expiry time.  
Once submitted, it becomes visible to nearby NGOs who can claim it instantly.  
Donors can track pickups, view past donations, and even sponsor meals financially.

## For NGOs (Receivers)
Registered NGOs can browse and claim available food donations in real time.  
Their dashboard provides live stats, delivery coordination, volunteer assignments, and donation history — ensuring every meal reaches someone in need efficiently.

## For Individuals (Supporters)
Anyone can contribute by donating food, sponsoring meals, or offering monetary support.  
Home donors can schedule pickups, while sponsors can fund pre-set meal packs.  
Every contribution directly feeds those in need and reflects in an impact summary for transparency.

## For Volunteers
Volunteers can register to assist with pickups and deliveries.  
They receive live tasks, track routes, and update statuses — ensuring timely and safe distribution.

## Smart Features
- AI Chatbot Assistant for quick help and guidance  
- Global search bar for seamless navigation  
- Notifications for donation and delivery updates  
- Interactive partner map showing active NGOs and restaurants  
- Secure online payment and live donation tracking  

## Vision
To build a connected ecosystem where compassion meets technology, turning every extra bite into a step toward ending hunger and food waste.

---

## 🏗️ Architecture

```
                    ┌─────────────────┐
                    │   Browser        │
                    │  http://localhost │
                    └────────┬────────┘
                             │
                    ┌────────▼────────┐
                    │   Frontend       │
                    │   (Nginx:80)     │
                    │   React SPA      │
                    └──┬──────────┬───┘
                       │          │
              Static   │          │  /api/*  /uploads/*
              files    │          │  (reverse proxy)
                       │   ┌──────▼──────┐
                       │   │  Backend     │
                       │   │  (Node:5001) │
                       │   │  Express API │
                       │   └──────┬──────┘
                       │          │
                       │   ┌──────▼──────┐
                       │   │  MongoDB     │
                       │   │  (27017)     │
                       │   │  Persistent  │
                       │   └─────────────┘
```

**Built Using the MERN Stack:**  
MongoDB for data storage, Express and Node.js for backend APIs, and React for a responsive frontend interface.

---

## 🐳 Docker Setup

### Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) (includes Docker Engine & Docker Compose)
- Git

### Quick Start

1. **Clone the repository** (if you haven't already):
   ```bash
   git clone <your-repo-url>
   cd MealConnect_
   ```

2. **Configure environment variables** (optional):
   ```bash
   # Copy the example env file
   cp .env.example .env

   # Edit .env and set your JWT_SECRET (recommended for production)
   # The default 'devsecret' works for local development
   ```

3. **Build and start the application:**
   ```bash
   docker compose up --build
   ```

4. **Open your browser:**
   - 🌐 **Frontend:** [http://localhost](http://localhost)
   - 🔌 **Backend API:** [http://localhost:5001/api/health](http://localhost:5001/api/health)

That's it! The complete application stack starts with a single command.

### Environment Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `JWT_SECRET` | `devsecret` | Secret key for JWT token signing. **Change in production!** |
| `PORT` | `5001` | Backend server port |
| `MONGO_URI` | `mongodb://mongo:27017/mealconnect` | MongoDB connection string (auto-configured for Docker) |
| `MONGO_DB` | `mealconnect` | MongoDB database name |
| `FRONTEND_URL` | `http://localhost` | Frontend URL for CORS configuration |

### Useful Docker Commands

```bash
# Build and start all services
docker compose up --build

# Start in detached mode (background)
docker compose up --build -d

# View running containers
docker compose ps

# View logs (all services)
docker compose logs

# View logs for a specific service
docker compose logs frontend
docker compose logs backend
docker compose logs mongo

# Follow logs in real-time
docker compose logs -f

# Stop all services
docker compose down

# Stop and remove volumes (⚠️ deletes database data!)
docker compose down -v

# Rebuild a specific service
docker compose build backend
docker compose build frontend

# Restart a specific service
docker compose restart backend
```

### Service Details

| Service | Container Name | Internal Port | External Port | Description |
|---------|---------------|---------------|---------------|-------------|
| `frontend` | mealconnect-frontend | 80 | 80 | Nginx serving React SPA + API reverse proxy |
| `backend` | mealconnect-backend | 5001 | 5001 | Express.js REST API |
| `mongo` | mealconnect-mongo | 27017 | (not exposed) | MongoDB 7 database |

### MongoDB Data Persistence

- Database data is stored in a Docker named volume (`mongo-data`).
- Uploaded files are stored in a Docker named volume (`backend-uploads`).
- Data **persists** across `docker compose down` and `docker compose up`.
- Data is **only deleted** if you explicitly run `docker compose down -v` (the `-v` flag removes volumes).

### Troubleshooting

**Containers won't start:**
```bash
# Check container status and health
docker compose ps

# Check logs for errors
docker compose logs
```

**Backend can't connect to MongoDB:**
- Ensure the `mongo` service is healthy: `docker compose ps`
- The backend waits for MongoDB to be ready before starting (via health check dependency)

**Frontend shows blank page or API errors:**
- Check that all 3 containers are running: `docker compose ps`
- Check backend logs: `docker compose logs backend`
- Verify the Nginx proxy is working: visit `http://localhost/api/health`

**Port 80 already in use:**
- Change the frontend port mapping in `docker-compose.yml`:
  ```yaml
  ports:
    - "3000:80"  # Use port 3000 instead
  ```
- Then access the app at `http://localhost:3000`

**Port 5001 already in use:**
- Change the backend port mapping in `docker-compose.yml`:
  ```yaml
  ports:
    - "5002:5001"  # Map to port 5002 externally
  ```

**Rebuild from scratch:**
```bash
docker compose down
docker compose build --no-cache
docker compose up
```

---

## 📁 Project Structure

```
MealConnect_/
├── backend/                    # Express.js API server
│   ├── config/db.js            # MongoDB connection
│   ├── controllers/            # Route handlers
│   ├── middleware/              # Auth & upload middleware
│   ├── models/                 # Mongoose schemas
│   ├── routes/                 # API route definitions
│   ├── uploads/                # File upload storage
│   ├── utils/                  # Helper utilities
│   ├── server.js               # Server entry point
│   ├── Dockerfile              # Backend Docker image
│   ├── .dockerignore           # Docker build exclusions
│   └── package.json
├── food-delivery-app/          # React frontend (CRA)
│   ├── public/                 # Static public assets
│   ├── src/
│   │   ├── components/         # React components
│   │   ├── context/            # Auth context
│   │   ├── pages/              # Page components
│   │   ├── styles/             # CSS stylesheets
│   │   ├── api.js              # Axios API client
│   │   └── App.js              # App router
│   ├── nginx.conf              # Nginx config for Docker
│   ├── Dockerfile              # Frontend Docker image
│   ├── .dockerignore           # Docker build exclusions
│   └── package.json
├── docker-compose.yml          # Docker Compose orchestration
├── .env.example                # Environment variable template
├── .gitignore                  # Git exclusions
└── README.md                   # This file
```

---

## 🛠️ Local Development (Without Docker)

If you prefer running without Docker:

1. **Backend:**
   ```bash
   cd backend
   npm install
   # Create .env with MONGO_URI pointing to your MongoDB
   npm run dev
   ```

2. **Frontend:**
   ```bash
   cd food-delivery-app
   npm install
   # Create .env with REACT_APP_API_URL=http://localhost:5001/api
   npm start
   ```
