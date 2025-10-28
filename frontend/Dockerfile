# Build stage
FROM node:20-alpine AS builder

WORKDIR /app

# Enable Corepack for Yarn 4.x support
RUN corepack enable

# Copy package files
COPY package.json yarn.lock .yarnrc.yml ./

# Install dependencies
RUN yarn install --frozen-lockfile

# Copy source code
COPY . .

# Runtime stage
FROM node:20-alpine

WORKDIR /app

# Enable Corepack for Yarn 4.x support
RUN corepack enable

# Copy from builder
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app /app

# Expose port (Vite default dev server)
EXPOSE 5173

# Start development server
CMD ["yarn", "start"]
