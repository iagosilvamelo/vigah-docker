FROM node:20-alpine

WORKDIR /app

COPY vigah-api/package*.json ./

RUN apk add --no-cache python3 make g++ && \
    npm install --production && \
    apk del python3 make g++

COPY vigah-api/ .

EXPOSE ${NODE_PORT}

CMD ["node", "server.js"]