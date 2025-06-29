---
title: "Face Recognition Django REST API"
description: "A Django REST Framework API for face detection, recognition, attendance marking, and user/profile management."
technologies:
  - Python 3
  - Django
  - Django REST Framework
  - dj-rest-auth
  - django-allauth
  - face_recognition
  - deepface
  - facenet-pytorch
  - PostgreSQL (or SQLite)
  - Docker (optional)
githubUrl: "https://github.com/Lokie-codes/Face-Recognition-Django-REST-API"
liveUrl: ""
featured: true
---

# Face Recognition Django REST API

A server-side application exposing RESTful endpoints to log in users, detect and recognize faces in images, mark attendance, and manage user profiles.

## Features

- **Authentication & Profiles**  
  - Email/password login & logout via `dj-rest-auth` & `django-allauth`  
  - Change password, token-based sessions, and user profile endpoints

- **Branch/Subject/Section Endpoints**  
  - List branches, subjects, and sections (e.g. for a school or organization)  

- **Face Detection & Recognition**  
  - Upload an image (`POST /face/recognise/`)  
  - Detect face bounding boxes and recognize known faces  
  - Returns list of user IDs (USNs) present in the image

- **Attendance Management**  
  - Fetch existing attendance records (`GET /api/attendance/`)  
  - Update attendance (`PUT /api/attendance/`)  
  - Send attendance report via email (`GET /api/send_mail/`)

- **Modular Design**  
  - Separate Django apps for API, face processing, attendance, etc.  
  - Pluggable backends: you can swap in custom face-recognition models

## Technical Highlights

- **Django & DRF**  
  Uses Django 4.1 and Django REST Framework 3.14 for quick API development and browsable API.

- **Authentication**  
  `dj-rest-auth` handles login/logout/password-change; `django-allauth` for registration and social auth hooks.

- **Face Algorithms**  
  - **Detection** via RetinaFace or Faster R-CNN + Feature Pyramid Network  
  - **Alignment** with 2D/3D fiducial-point warping  
  - **Recognition** using a small CNN (convolution + pooling + fully connected) to produce embedding vectors  
  - **Similarity** measured with weighted-χ² (Chi-square) metric

- **Dependencies**  
  Leverages `face_recognition`, `deepface`, and `facenet-pytorch` to encapsulate state-of-the-art face pipelines.

- **Deployment**  
  - Local development with SQLite (default) or switch to PostgreSQL  
  - Optional Dockerization—just add a `Dockerfile` and `docker-compose.yml`  
  - Included `.ebextensions/` for AWS Elastic Beanstalk deployments  
