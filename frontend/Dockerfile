FROM node:20-alpine AS deps
WORKDIR /app

# Install dependencies based on the lockfile
COPY package.json package-lock.json ./
RUN npm ci --production=false --no-progress --prefer-offline

# Build the Next.js app
FROM node:20-alpine AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

# Production image with minimal footprint
FROM node:20-alpine AS runner
WORKDIR /app
ENV NODE_ENV=production

# Copy the built app
COPY --from=builder /app/.next ./.next
COPY --from=builder /app/public ./public
COPY --from=builder /app/package.json ./package.json
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/next.config.js ./next.config.js
COPY --from=builder /app/tsconfig.json ./tsconfig.json

EXPOSE 3000

CMD ["npm", "start"]