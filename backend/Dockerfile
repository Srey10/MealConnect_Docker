FROM node:20-alpine

WORKDIR /app

# Create uploads directory
RUN mkdir -p uploads/menu-items

# Copy dependency manifests
COPY package.json package-lock.json ./

# Install production dependencies only
RUN npm ci --only=production

# Copy application source
COPY . .

# Expose port
EXPOSE 5001

# Health check
HEALTHCHECK --interval=30s --timeout=10s --start-period=40s --retries=3 \
  CMD wget --no-verbose --tries=1 --spider http://localhost:5001/api/health || exit 1

# Start the server
CMD ["node", "server.js"]
