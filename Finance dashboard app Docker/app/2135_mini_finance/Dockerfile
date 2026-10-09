
# Stage 1: Prepare the static website
FROM alpine:3.22 AS build

WORKDIR /site
COPY . .

# Remove project-only files from the website output
RUN rm -f /site/Dockerfile \
    /site/.dockerignore \
    /site/README-DOCKER.md

# Stage 2: Production web server
FROM nginx:alpine AS production

COPY --from=build /site/ /usr/share/nginx/html/

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
