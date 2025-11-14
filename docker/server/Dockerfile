FROM node:20-slim

WORKDIR /app

COPY package*.json ./
RUN npm install --omit=dev

COPY public ./public
COPY server.js ./server.js

ENV NODE_ENV=production
ENV PORT=9000

EXPOSE 9000

CMD ["node", "server.js"]
