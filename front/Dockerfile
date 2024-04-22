FROM node:16-alpine AS builder

COPY . /app
WORKDIR /app

ARG VITE_API_URL
ARG VITE_MAPBOX_TOKEN
ARG VITE_IMAGEKIT_URL_ENDPOINT
ARG VITE_SERVER_URL
ARG VITE_GOOGLE_MAPS_API_KEY

COPY . /app
WORKDIR /app

RUN echo ${VITE_API_URL}
RUN echo ${VITE_MAPBOX_TOKEN}
RUN echo ${VITE_IMAGEKIT_URL_ENDPOINT}
RUN echo ${VITE_SERVER_URL}
RUN echo ${VITE_GOOGLE_MAPS_API_KEY}

RUN npm ci && npm run build-only

#FROM nginx
#
#COPY ./docker/default.conf /etc/nginx/conf.d/default.conf
#COPY --from=builder /app/dist /var/www/html

FROM nginx:alpine AS production-build

## Remove default nginx index page
RUN rm -rf /usr/share/nginx/html/*

COPY ./docker/default.conf /etc/nginx/nginx.conf

COPY --from=builder /app/dist /usr/share/nginx/html

EXPOSE 5000