# LMS-DevEnv
Development Environment that connects frontend, backend and database

## Overview
This repository provides a complete development environment for the Loan Management System (LMS) using Docker Compose. It integrates:
- **Frontend**: React application
- **Backend**: Django REST API
- **Database**: PostgreSQL

## Prerequisites
- Docker (version 20.10 or higher)
- Docker Compose (version 2.0 or higher)

## Project Structure
```
LMS-DevEnv/
├── backend/              # Django backend application
│   ├── Dockerfile
│   ├── requirements.txt
│   └── .env.example
├── frontend/             # React frontend application
│   ├── Dockerfile
│   ├── package.json
│   └── .env.example
├── .github/
│   └── workflows/
│       └── ci-cd.yml    # GitHub Actions workflow
└── docker-compose.yml   # Docker Compose configuration
```

## Quick Start

### 1. Clone the Repository
```bash
git clone https://github.com/Loan-Management-System-HQ/LMS-DevEnv.git
cd LMS-DevEnv
```

### 2. Set Up Environment Variables
```bash
# Backend
cp backend/.env.example backend/.env

# Frontend
cp frontend/.env.example frontend/.env
```

### 3. Start the Development Environment
```bash
docker-compose up --build
```

This will start all three services:
- **PostgreSQL**: Available at `localhost:5432`
- **Django Backend**: Available at `http://localhost:8000`
- **React Frontend**: Available at `http://localhost:3000`

## Service Details

### Database (PostgreSQL)
- **Container**: lms-postgres
- **Port**: 5432
- **Default Credentials**:
  - Database: `lms_db`
  - User: `lms_user`
  - Password: `lms_password`

### Backend (Django)
- **Container**: lms-backend
- **Port**: 8000
- **Health Check**: Service starts after database is healthy
- **Volume**: `./backend` mounted to `/app` for hot-reloading

### Frontend (React)
- **Container**: lms-frontend
- **Port**: 3000
- **Development Server**: Includes hot-reloading
- **API URL**: Configured to connect to backend at `http://localhost:8000`

## Docker Commands

### Start Services
```bash
docker-compose up
```

### Start Services in Background
```bash
docker-compose up -d
```

### Stop Services
```bash
docker-compose down
```

### View Logs
```bash
# All services
docker-compose logs -f

# Specific service
docker-compose logs -f backend
docker-compose logs -f frontend
docker-compose logs -f db
```

### Rebuild Images
```bash
docker-compose build --no-cache
```

### Execute Commands in Containers
```bash
# Django management commands
docker-compose exec backend python manage.py migrate
docker-compose exec backend python manage.py createsuperuser

# Access PostgreSQL
docker-compose exec db psql -U lms_user -d lms_db

# Access backend shell
docker-compose exec backend bash

# Access frontend shell
docker-compose exec frontend sh
```

## Development Workflow

### Backend Development
1. Make changes to Django code in `./backend`
2. Changes are automatically reflected (hot-reload enabled)
3. Run migrations: `docker-compose exec backend python manage.py migrate`
4. Create migrations: `docker-compose exec backend python manage.py makemigrations`

### Frontend Development
1. Make changes to React code in `./frontend`
2. Changes are automatically reflected (hot-reload enabled)
3. Install new packages: 
   ```bash
   docker-compose exec frontend npm install <package-name>
   ```

### Database Management
- Access PostgreSQL shell: `docker-compose exec db psql -U lms_user -d lms_db`
- Backup database: `docker-compose exec db pg_dump -U lms_user lms_db > backup.sql`
- Restore database: `docker-compose exec -T db psql -U lms_user -d lms_db < backup.sql`

## CI/CD Pipeline

The repository includes a GitHub Actions workflow (`.github/workflows/ci-cd.yml`) that:
- Runs backend tests with PostgreSQL
- Runs frontend tests and builds
- Validates Docker Compose configuration
- Performs security scanning with Trivy

## Troubleshooting

### Port Already in Use
If you encounter port conflicts, modify the port mappings in `docker-compose.yml`:
```yaml
ports:
  - "3001:3000"  # Change first number to use different host port
```

### Database Connection Issues
1. Ensure database service is healthy: `docker-compose ps`
2. Check logs: `docker-compose logs db`
3. Verify environment variables in `backend/.env`

### Permission Issues
On Linux, you may need to adjust file permissions:
```bash
sudo chown -R $USER:$USER ./backend ./frontend
```

## Contributing
1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

## License
See LICENSE file for details
