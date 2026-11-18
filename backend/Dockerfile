FROM node:22.23.5-alpine3.24

WORKDIR /app

COPY package.json package-lock.json ./

RUN npm ci

COPY --chown=node:node server.js ./

USER node

EXPOSE 3007

CMD ["npm", "run", "dev"]