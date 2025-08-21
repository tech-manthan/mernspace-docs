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

#### RUN DOCKER IMAGE

```bash
sudo docker run --env-file .env.dev -e PRIVATE_KEY=""  --network host   -p 5501:5501   techmanthan/mernspace_auth_service:build-23
```

```bash
sudo docker run --env-file .env.prod -e PRIVATE_KEY=""  --add-host=host.docker.internal:host-gateway -p 5501:5501   techmanthan/mernspace_auth_service:build-23
```