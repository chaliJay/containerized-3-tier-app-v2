# Containerized Web Application with Redis and MySQL

## Project Overview
This project extends the three-tier containerized application by adding a Redis cache.
It includes:

Frontend – web interface

Backend – Flask API

MySQL database with persistent storage (PVC)

Redis – page-view counter/cache

Redis runs as a separate standalone pod and service named cache. It is not part of the backend pod.

## System Architecture Diagram

                    ┌───────────────┐
                    │    Frontend   │
                    │   Port 80     │
                    └───────┬───────┘
                            │
                            ▼
                    ┌───────────────┐
                    │    Backend    │
                    │   Port 8000   │
                    └───────┬───────┘
                            │
                 ┌──────────┴──────────┐
                 │                     │
                 ▼                     ▼
        ┌────────────────┐    ┌────────────────┐
        │     MySQL      │    │     Redis      │
        │    Port 3306   │    │    Port 6379   │
        │                │    │                │
        │ Persistent PVC │    │  Non-persistent│
        └────────────────┘    └────────────────┘


The backend communicates with both MySQL and Redis.

## Redis Configuration

Redis is deployed separately using the redis:7-alpine image.
The backend increments the page-view counter using Redis:
```
def visits(): 
    count = r.incr('page_views') 
    return jsonify(visits=count)
```

## Building & Publishing Docker Images

Backend
Build the backend image:
```
docker build -t <dockerhub-username>/backend:v1 .
```

Push it:
```
docker push <dockerhub-username>/backend:v1
```

Frontend
Build the frontend image:
```
docker build -t <dockerhub-username>/frontend:v1 .
```

Push it:
```
docker push <dockerhub-username>/frontend:v1
```

These image tags are referenced in the Rahti deployment YAML files.

You will get a URL like: https://frontend-route-tta-v2.2.rahtiapp.fi/

This project demonstrates the difference between non-persistent cache data and persistent database data in a containerized OpenShift application.
