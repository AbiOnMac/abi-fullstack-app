# Use Node 23 Alpine base image
FROM node:23-alpine3.20 AS base

# Install necessary build tools
RUN apk add --no-cache g++ make py3-pip libc6-compat

# Set working directory
WORKDIR /app

# Copy package files first to leverage Docker caching
COPY package*.json ./

# Expose the application port
EXPOSE 3000

# ----------------------------
# Builder Stage
# ----------------------------
FROM base AS builder
WORKDIR /app
COPY . .
RUN npm install
RUN npm run build

# ----------------------------
# Production Stage
# ----------------------------
FROM base AS production
WORKDIR /app

ENV NODE_ENV=production

# Clean and install only production dependencies
RUN npm ci --omit=dev

# Create non-root user
RUN addgroup -g 1001 -S nodejs \
  && adduser -S nextjs -u 1001

USER nextjs

# Copy build artifacts and assets
COPY --from=builder --chown=nextjs:nodejs /app/.next ./.next
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package.json ./package.json
COPY --from=builder /app/public ./public

# Start the app
CMD ["npm", "start"]

# ----------------------------
# Development Stage
# ----------------------------
FROM base AS dev
WORKDIR /app

ENV NODE_ENV=development

# Install all dependencies (including dev)
RUN npm install

COPY . .

CMD ["npm", "run", "dev"]
