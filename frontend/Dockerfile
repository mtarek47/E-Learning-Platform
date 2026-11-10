# ============================================================
# Stage 1 — Build the React app
# ============================================================
FROM node:20-alpine AS builder

WORKDIR /app

# Install dependencies first (layer cache optimisation)
COPY package.json package-lock.json ./
RUN npm ci --silent

# Copy source and build
COPY . .
RUN npm run build

# ============================================================
# Stage 2 — Serve with Nginx
# ============================================================
FROM nginx:1.27-alpine

# Remove default nginx page
RUN rm -rf /usr/share/nginx/html/*

# Copy React build output
COPY --from=builder /app/build /usr/share/nginx/html

# Custom nginx config (handles React Router)
COPY nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
