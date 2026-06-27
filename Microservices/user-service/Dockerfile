# Use Node.js official image
FROM node:20-alpine

# Set working directory
WORKDIR /app

# Copy dependency files
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy application code
COPY . .

# Expose service port
EXPOSE 3000

# Start application
CMD ["node", "app.js"]