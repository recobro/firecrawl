# ─── Stage 1: Builder – compila tu aplicación + descarga Chromium ───
FROM node:18-slim AS builder

# Instala librerías del sistema necesarias para Chromium
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

# Copia dependencias
COPY package*.json ./
RUN npm install

# Copia el código fuente
COPY . .

# Define dónde se instalarán los navegadores
ENV PLAYWRIGHT_BROWSERS_PATH=/ms-playwright

# Instala Chromium + dependencias para Playwright
RUN npx playwright install --with-deps chromium

# Compila TypeScript
RUN npm run build

# ─── Stage 2: Producción – imagen liviana con solo lo necesario ───
FROM node:18-slim AS runtime
WORKDIR /usr/src/app

# Copia artefactos compilados + node_modules (y binarios de Chromium) desde el builder
COPY --from=builder /usr/src/app/dist ./dist
COPY --from=builder /usr/src/app/node_modules ./node_modules
COPY --from=builder /ms-playwright /ms-playwright

# Si usas variables de entorno para puerto:
ARG PORT
ENV PORT=${PORT:-3000}

EXPOSE ${PORT}

# Comando por defecto
CMD ["node", "dist/api.js"]
