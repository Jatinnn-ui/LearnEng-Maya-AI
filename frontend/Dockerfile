# EngLearn.ai frontend — build React app, serve static files with nginx
# ---- Build stage ----
FROM node:20-alpine AS build

WORKDIR /app

# REACT_APP_* vars are baked in at build time (CRA), so pass this as a build arg.
# e.g. docker build --build-arg REACT_APP_BACKEND_URL=https://your-api.example.com .
ARG REACT_APP_BACKEND_URL=""
ENV REACT_APP_BACKEND_URL=$REACT_APP_BACKEND_URL

# Install dependencies first for better layer caching
COPY package.json yarn.lock ./
RUN yarn install --frozen-lockfile --network-timeout 600000

# Build the production bundle
COPY . .
RUN yarn build

# ---- Serve stage ----
FROM nginx:1.27-alpine AS serve

# SPA routing + optional API/WebSocket proxy
COPY nginx.conf /etc/nginx/conf.d/default.conf
COPY --from=build /app/build /usr/share/nginx/html

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
