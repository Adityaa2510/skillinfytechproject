# Mini Finance Dashboard — Docker

This is a static HTML/CSS/JavaScript dashboard served by Nginx. It does not require Node.js or a backend for static hosting.

## Build the image

From this directory:

```bash
docker build -t mini-finance-dashboard:1.0 .
```

## Run the container

```bash
docker run -d --name mini-finance-dashboard -p 8080:80 --restart unless-stopped mini-finance-dashboard:1.0
```

Open http://localhost:8080 in a browser.

## Verify and troubleshoot

```bash
docker ps
docker logs mini-finance-dashboard
docker exec mini-finance-dashboard nginx -t
```

Stop and remove the container:

```bash
docker stop mini-finance-dashboard
docker rm mini-finance-dashboard
```

## Push to Docker Hub

Replace `YOUR_DOCKERHUB_USERNAME` with your Docker Hub username:

```bash
docker login
docker tag mini-finance-dashboard:1.0 YOUR_DOCKERHUB_USERNAME/mini-finance-dashboard:1.0
docker push YOUR_DOCKERHUB_USERNAME/mini-finance-dashboard:1.0
```

## Notes

- The original dashboard files are preserved.
- This image serves the static frontend only. Any template forms or finance interactions that require a backend/database remain demo-only until implemented.
- Run these Docker commands in the environment where Docker Engine is installed.
