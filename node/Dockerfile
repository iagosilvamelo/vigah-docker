FROM node:20-alpine

WORKDIR /app

COPY vigah-api/package*.json ./

RUN apk add --no-cache python3 make g++ && \
    npm install --production && \
    apk del python3 make g++ && \
    apk add --no-cache postgresql-client

COPY vigah-api/ .

RUN npm install --global @adonisjs/cli
RUN npm install

EXPOSE ${NODE_PORT}

CMD ["npm", "run", "dev"]