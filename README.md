# Docker Project: Building a Scalable Web Application Infrastructure 🐳

A step‑by‑step journey from a single “Hello, World!” Docker image to a **multi‑container, horizontally scalable web application** featuring:

* Front-end (static site on Nginx)
* Back-end API (Python + Flask) scaled to multiple instances
* Reverse proxy / load balancer (Nginx, round‑robin)

---

## ✨ Key Concepts Practiced

| Topic | Covered In |
|-------|------------|
| Writing a Dockerfile | task0, task1, task2 |
| Multi-stage / lightweight images (concept intro) | task2+ |
| Networking between containers | task3 |
| Docker Compose orchestration | task4 |
| Reverse proxy (Nginx) | task5 |
| Horizontal scaling & load balancing | task6 |

---

## 🧰 Technologies

* Docker & Docker Compose
* Nginx (static serving + reverse proxy + load balancing)
* Python 3 + Flask
* HTML / CSS / JavaScript (Softy Pinko template)
* (Optional) curl / jq for testing

---

## 📁 Repository Structure

```
holbertonschool-softy-pinko-docker/
├── task0/            # Basic Ubuntu-based image printing Hello World
├── task1/            # Flask API container
├── task2/            # Static front-end container (Nginx)
├── task3/            # Front-end consuming API over container network
├── task4/            # docker-compose.yml introduced
├── task5/            # Added reverse proxy layer
└── task6/            # Final architecture with load-balanced back-end
    ├── back-end/
    │   ├── Dockerfile
    │   └── api.py
    ├── front-end/
    │   ├── Dockerfile
    │   ├── softy-pinko-front-end/
    │   └── softy-pinko-front-end.conf
    ├── proxy/
    │   ├── Dockerfile
    │   └── proxy.conf
    ├── 2-api-servers.txt
    └── docker-compose.yml
```

---

## 🧪 Task-by-Task Overview

1. **Task 0 – First Image**  
   Minimal Ubuntu image that echoes “Hello, World!” (foundation for syntax & layering).
2. **Task 1 – Back-end**  
   Flask API (`/api/hello`) containerized; exposes simple JSON/text response.
3. **Task 2 – Front-end**  
   Static assets served via Nginx container.
4. **Task 3 – Connect Services**  
   Front-end makes AJAX/fetch call to Flask API over Docker network.
5. **Task 4 – Compose**  
   Introduced `docker-compose.yml` to orchestrate services together.
6. **Task 5 – Reverse Proxy**  
   Added an entrypoint Nginx that routes:
   * `/` → static front-end
   * `/api/*` → back-end
7. **Task 6 – Scaling**  
   Scaled back-end to 2 instances; reverse proxy balances traffic (round-robin).

---

## 🚀 Quick Start (Final Architecture – task6)

### Prerequisites
* Docker (Desktop or Engine) installed: https://www.docker.com/products/docker-desktop
* (Optional) Make sure ports 80 (and 5000 if directly testing) are free.

### Clone
```bash
git clone https://github.com/Amedjnoun/holbertonschool-softy-pinko-docker.git
cd holbertonschool-softy-pinko-docker/task6
```

### Launch (build + run + scale)
```bash
docker-compose up --build --scale back-end=2
```

### Access
* App: http://localhost (site + dynamic API text)
* API direct (through proxy): http://localhost/api/hello

### Stop & Clean
```bash
# In the running terminal: CTRL + C
docker-compose down
```

Add `-v` to remove named/anonymous volumes if any appear later:
```bash
docker-compose down -v
```

---

## 🔁 Observing Load Balancing

Refreshing the page triggers alternating API hits:

```log
task6-back-end-1  | 172.20.0.5 - - [15/Jun/2023 19:53:55] "GET /api/hello HTTP/1.0" 200 -
task6-back-end-2  | 172.20.0.5 - - [15/Jun/2023 19:53:57] "GET /api/hello HTTP/1.0" 200 -
task6-back-end-1  | 172.20.0.5 - - [15/Jun/2023 19:53:58] "GET /api/hello HTTP/1.0" 200 -
task6-back-end-2  | 172.20.0.5 - - [15/Jun/2023 19:53:58] "GET /api/hello HTTP/1.0" 200 -
```

You can also hit the API repeatedly:
```bash
for i in {1..6}; do curl -s http://localhost/api/hello; echo; done
```

---

## 🧩 Per-Task Usage Samples

Below are representative (simplified) commands you might run inside each task directory:

```bash
# Task 0
cd task0
docker build -t hello-world-img .
docker run --rm hello-world-img

# Task 1
cd ../task1
docker build -t flask-api .
docker run -p 5000:5000 flask-api

# Task 2
cd ../task2
docker build -t softy-fe .
docker run -p 8080:80 softy-fe

# Task 3
cd ../task3
docker network create appnet
# build images
docker build -t flask-api ./back-end
docker build -t softy-fe ./front-end
# run with shared network and link via hostname
docker run -d --name api --network appnet flask-api
docker run -d -p 8080:80 --name fe --network appnet -e API_HOST=api softy-fe

# Task 4
cd ../task4
docker-compose up --build

# Task 5
cd ../task5
docker-compose up --build

# Task 6
cd ../task6
docker-compose up --build --scale back-end=2
```

---

## ⚙️ Configuration & Customization

### Scaling Back-end Count
```bash
docker-compose up -d --scale back-end=4
```
Then reload the app to see 4-way rotation.

### Changing the API Endpoint Path
Adjust `proxy/proxy.conf` upstream location or Flask route in `api.py`.

### Environment Variables (Potential Enhancements)
You can parameterize:
* API response message
* Front-end base URL
* Proxy health check path

Example extension (compose `environment:`):
```yaml
environment:
  API_MESSAGE: "Hello from container"
```

And in `api.py`:
```python
import os
message = os.getenv("API_MESSAGE", "Hello, World!")
```

### Logs
Follow specific service logs:
```bash
docker-compose logs -f proxy
docker-compose logs -f back-end
```

---

## 🛡️ Production Hardening Ideas (Not yet implemented)

* Use slimmer base images (e.g. `python:3.12-slim`, `nginx:alpine`)
* Multi-stage build for Python dependencies
* Add health checks in `docker-compose.yml`
* Rate limiting & security headers in Nginx
* Enable caching for static assets
* Add CI pipeline (lint, build, scan)
* Parameterize secrets via `.env` (excluded from VCS)
* Add structured logging (JSON) & metrics endpoint

---

## 🧪 Testing Ideas

| Type | Example |
|------|---------|
| Liveness | `curl -I http://localhost/api/hello` |
| Round-robin distribution | Repeated curl loop & observe logs |
| Failure simulation | Stop one back-end: `docker-compose stop back-end-1` |
| Front-end integration | Open DevTools Network panel to inspect `/api/hello` response |

---

## 🧯 Troubleshooting

| Symptom | Possible Cause | Fix |
|---------|----------------|-----|
| 502 Bad Gateway | Back-end containers not healthy / wrong upstream host | Check `docker-compose ps` and proxy config |
| Front-end shows stale message | Browser cache | Hard refresh / disable cache |
| Port already in use | Another service on :80 | Edit compose to map e.g. `8080:80` |
| Requests not alternating | Only one back-end instance | Scale with `--scale back-end=2` |

Check container networks:
```bash
docker network ls
docker network inspect task6_default
```

---

## 🧠 Learning Outcomes

By finishing this project you will be comfortable with:
* Building Docker images from scratch
* Networking between containers
* Service orchestration with Compose
* Introducing a reverse proxy and central entry point
* Horizontal scaling of stateless services
* Observing runtime behavior via logs

---

## 🗂️ Possible Next Steps

* Add a database layer (PostgreSQL)
* Introduce Redis caching
* Add automated tests (pytest) and run in CI
* Replace round-robin with least-connections or sticky sessions
* Deploy to a remote host or Kubernetes

---

## 📜 License

Add your preferred license (e.g. MIT) in a `LICENSE` file.

---

## 🤝 Contributing

1. Fork
2. Create feature branch: `git checkout -b feature/xyz`
3. Commit: `git commit -m "Add xyz"`
4. Push: `git push origin feature/xyz`
5. Open a Pull Request

---

## 🙋 Support / Questions

Feel free to open an issue or discussion if you extend this project or run into problems.

Happy containerizing! 🚀

