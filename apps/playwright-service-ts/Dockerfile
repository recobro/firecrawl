FROM node:18-slim

# Instalar dependencias del sistema necesarias para Chromium
RUN apt-get update && apt-get install -y \
    ca-certificates \
    fonts-liberation \
    libasound2 \
    libatk-bridge2.0-0 \
    libatk1.0-0 \
    libcups2 \
    libdbus-1-3 \
    libdrm2 \
    libgbm1 \
    libglib2.0-0 \
    libgtk-3-0 \
    libnspr4 \
    libnss3 \
    libx11-6 \
    libxcomposite1 \
    libxdamage1 \
    libxext6 \
    libxfixes3 \
    libxrandr2 \
    libxrender1 \
    libxss1 \
    libxtst6 \
    libpci3 \
    --no-install-recommends \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*

WORKDIR /usr/src/app

# Copiar package.json (y lockfile si existe), instalar dependencias
COPY package*.json ./
RUN npm install

# Copiar resto de código
COPY . .

# Definir path donde instalará los navegadores
ENV PLAYWRIGHT_BROWSERS_PATH=/ms-playwright

# Instalar chromium + sus dependencias usando Playwright
RUN npx playwright install --with-deps chromium

# (Opcional) limpia cachés npm / node_modules innecesarios para reducir tamaño
RUN npm prune --production

# Construir tu app (si tienes build step)
RUN npm run build

ARG PORT
ENV PORT=${PORT:-3000}

EXPOSE ${PORT}

# Ajuste: lanzar como root o crear usuario no-root si prefieres
CMD ["npm", "start"]
