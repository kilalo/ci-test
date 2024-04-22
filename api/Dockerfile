FROM node:20-alpine AS development
ENV NODE_ENV development
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install
COPY . .

FROM node:20-alpine AS builder
# RUN apk add build-base # This line installs the build-base package using the Alpine package manager (apk). This package includes the necessary tools for building native Node.js modules.
#RUN apk add python3 # This line installs Python 3 using the Alpine package manager. Python is required for some Node.js modules that have native dependencies.
#RUN npm install -g node-gyp # This line installs the node-gyp package globally using npm. node-gyp is a tool for building native Node.js modules.
WORKDIR /usr/src/app
COPY --chown=node:node package*.json ./
COPY --chown=node:node --from=development /usr/src/app/node_modules ./node_modules
COPY --chown=node:node . .
RUN npx prisma generate
RUN npm run prebuild && npm run build
ENV NODE_ENV production
RUN npm ci --only=production && npm cache clean --force
USER node

FROM node:20-alpine AS production
WORKDIR /app
COPY --chown=node:node --from=builder /usr/src/app/node_modules ./node_modules
COPY --chown=node:node --from=builder /usr/src/app/dist ./dist
COPY --chown=node:node --from=builder /usr/src/app/prisma ./prisma
COPY --chown=node:node --from=builder /usr/src/app/package*.json ./

CMD npx prisma migrate deploy && npm run start:prod