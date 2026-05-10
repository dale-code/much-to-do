# Much-To-Do Application

A full-stack task management application built with React (frontend) and Go (backend), deployed on AWS.

## Live URLs

- **Frontend:** https://d140111d6o7txc.cloudfront.net
- **Backend API:** http://much-to-do-alb-567734065.us-east-1.elb.amazonaws.com

## Application Stack

| Layer | Technology |
|---|---|
| Frontend | React + Vite + TailwindCSS |
| Backend | Go + Gin framework |
| Database | MongoDB 7 |
| Cache | Redis (AWS ElastiCache) |

## Repository Structure
much-to-do/
├── Client/                          # React frontend
│   ├── src/
│   ├── .env.example
│   └── vite.config.ts
├── Server/
│   └── MuchToDo/                   # Go backend
│       ├── cmd/api/main.go
│       ├── internal/
│       │   ├── auth/
│       │   ├── cache/
│       │   ├── config/
│       │   ├── database/
│       │   ├── handlers/
│       │   ├── middleware/
│       │   ├── models/
│       │   └── routes/
│       ├── .env.example
│       └── go.mod
└── .github/
└── workflows/
├── deploy-frontend.yml      # Frontend CI/CD
└── deploy-backend.yml       # Backend CI/CD

## Environment Variables

### Backend (Server/MuchToDo/.env)

| Variable | Description | Default |
|---|---|---|
| `PORT` | Server port | `8080` |
| `MONGO_URI` | MongoDB connection string | - |
| `DB_NAME` | MongoDB database name | `much_todo_db` |
| `JWT_SECRET_KEY` | JWT signing secret | - |
| `JWT_EXPIRATION_HOURS` | JWT lifetime in hours | `72` |
| `ENABLE_CACHE` | Enable Redis caching | `false` |
| `REDIS_ADDR` | Redis host:port | - |
| `REDIS_PASSWORD` | Redis password | - |
| `LOG_LEVEL` | Log level | `INFO` |
| `LOG_FORMAT` | Log format (json/text) | `json` |
| `ALLOWED_ORIGINS` | CORS allowed origins | `http://localhost:5173` |

### Frontend (Client/.env)

| Variable | Description |
|---|---|
| `VITE_API_BASE_URL` | Backend API URL |

## CI/CD Pipeline

Both pipelines are defined in `.github/workflows/` and trigger on:
- Push to `main` or `feature/full-stack` branch
- Manual workflow dispatch

### Frontend Pipeline (`deploy-frontend.yml`)

1. Checkout code
2. Setup Node.js 20
3. Install dependencies (`npm ci`)
4. Build React app with `VITE_API_BASE_URL` injected
5. Deploy build artifacts to S3
6. Invalidate CloudFront cache

### Backend Pipeline (`deploy-backend.yml`)

1. Checkout code
2. Setup Go 1.21
3. Build Go binary for Linux (`GOOS=linux GOARCH=amd64`)
4. Upload binary to S3
5. Deploy to EC2 instances via AWS SSM
6. Verify deployment by hitting `/ping` endpoint

## Required GitHub Secrets

| Secret | Description |
|---|---|
| `AWS_ACCESS_KEY_ID` | AWS access key |
| `AWS_SECRET_ACCESS_KEY` | AWS secret key |
| `AWS_REGION` | AWS region |
| `S3_BUCKET_NAME` | Frontend S3 bucket name |
| `CLOUDFRONT_DISTRIBUTION_ID` | CloudFront distribution ID |
| `ALB_DNS_NAME` | ALB DNS name |
| `EC2_INSTANCE_1_ID` | Backend EC2 instance 1 ID |
| `EC2_INSTANCE_2_ID` | Backend EC2 instance 2 ID |

## Local Development

### Backend

```bash
cd Server/MuchToDo
cp .env.example .env
# Fill in your .env values
go run ./cmd/api/
```

### Frontend

```bash
cd Client
cp .env.example .env
# Set VITE_API_BASE_URL=http://localhost:8080
npm install
npm run dev
```

## Infrastructure

All infrastructure is managed via Terraform in the separate infra repository:
[much-to-do-infra](https://github.com/dale-code/much-to-do-infra)
