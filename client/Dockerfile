FROM node:20-alpine as build-stage

WORKDIR /app

COPY vigah-client/package*.json ./
RUN npm install

COPY vigah-client/ .

RUN npm run build

FROM nginx:alpine as production-stage

RUN rm /etc/nginx/conf.d/default.conf

COPY docker/client/nginx.conf /etc/nginx/conf.d/default.conf

COPY --from=build-stage /app/dist /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]