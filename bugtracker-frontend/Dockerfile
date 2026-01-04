# --- Stage 1: Build ---
# The "builder" stage installs dependencies and builds the Next.js app.
FROM node:20-alpine AS builder

WORKDIR /app

# Accept a build argument, needed for deploying on Fly.io
ARG NEXT_PUBLIC_API_URL

# Copy package.json and package-lock.json (or yarn.lock)
COPY package*.json ./

# Use npm ci for faster, more reliable, and reproducible builds
RUN npm ci

# Copy the rest of the source code
COPY . .

# Build the Next.js app
RUN npm run build

# --- Stage 2: Production ---
# The "production" stage creates the final, lean image.
FROM node:20-alpine AS production

WORKDIR /app

# Create a non-root user for security best practices
RUN addgroup -g 1001 -S nodejs && adduser -S nextjs -u 1001


# Copy only the necessary production artifacts from the "builder" stage
COPY --from=builder /app/public ./public
COPY --from=builder --chown=nextjs:nodejs /app/.next ./.next
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package.json ./package.json

USER nextjs

EXPOSE 3000

CMD ["npm", "start"]