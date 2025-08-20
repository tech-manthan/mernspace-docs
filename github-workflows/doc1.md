## CI Workflow

ci.yaml

```bash
name: CI
on:
  pull_request:
    branches:
      - main

  push:
    branches:
      - main

jobs:
  build-and-test:
    name: Build the project
    runs-on: ubuntu-latest
    # if: github.event_name == 'pull_request
    steps:
      - uses: actions/checkout@v4
      - name: Install dependencies
        run: npm ci
      - name: Run eslint
        run: npm run lint:fix
      - name: Test and coverage
        run: npm run test

        env:
          DB_HOST: ${{ secrets.TEST_DB_HOST }}
          DB_PORT: ${{ secrets.TEST_DB_PORT }}
          DB_USERNAME: ${{ secrets.TEST_DB_USERNAME }}
          DB_PASSWORD: ${{ secrets.TEST_DB_PASSWORD }}
          DB_NAME: ${{ secrets.TEST_DB_NAME }}
          REFRESH_TOKEN_SECRET: ${{ secrets.REFRESH_TOKEN_SECRET }}
          ACCESS_MAX_AGE: ${{ secrets.ACCESS_MAX_AGE }}
          REFRESH_MAX_AGE: ${{ secrets.REFRESH_MAX_AGE }}
          JWKS_URI: ${{ secrets.JWKS_URI }}
          PRIVATE_KEY: ${{ secrets.PRIVATE_KEY }}
      - name: Build-ts
        run: npm run build
      - name: SonarCloud Scan
        uses: SonarSource/sonarcloud-github-action@master
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}

  build-and-push-docker:
    name: Build the Push Docker Image
    needs: build-and-test
    runs-on: ubuntu-latest
    env:
      IMAGE_NAME: techmanthan/auth_service
      IMAGE_TAG: build-${{ github.run_number }}
    if: github.ref == 'refs/heads/main' && github.event_name == 'push'
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
      - name: Log in to Docker hub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_PASSWORD }}
      - name: Build Docker Image
        run: docker build -t ${{ env.IMAGE_NAME }}:${{ env.IMAGE_TAG }} -f docker/prod/Dockerfile .
      - name: Push Docker Image to Docker hub
        run: docker push ${{ env.IMAGE_NAME }}:${{ env.IMAGE_TAG }}

```
