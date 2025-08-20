## Application PRODUCTION IMAGE

#### Docker File

```bash
FROM node:24-alpine3.21 AS builder

WORKDIR /app

COPY package*.json ./

RUN npm ci

COPY . .

RUN npm run build



FROM node:24-alpine3.21 AS production

ENV NODE_ENV=production

WORKDIR /app

COPY package*.json ./

RUN npm ci --ignore-scripts

COPY --from=builder /app/dist ./

EXPOSE 5500

CMD [ "node","server.js" ]
```

#### BUILD COMMAND

```bash
sudo docker build -t mernspace_test_prod_image
-f docker/prod/Dockerfile .
```
