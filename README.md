# Alpine
Alpine-hummus
name: Build and Tag Docker Image

on:
  push:
    branches:
      - main
      
jobs:
  build-and-tag:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Setup JFrog CLI
        uses: jfrog/setup-jfrog-cli@v3
        env:
          JF_URL: ${{ secrets.JF_URL }}
          JF_ACCESS_TOKEN: ${{ secrets.JF_ACCESS_TOKEN }}

      - name: Build Tag and push Docker Image
        env:
          IMAGE_NAME: mohamadspilot.jfrog.io/docker/jfrog-docker-example-image:${{ github.run_number }}
        run: |
          jf docker build -t $IMAGE_NAME .
          jf docker push $IMAGE_NAME
          
      - name: Publish Build info With JFrog CLI
        env:
          # Generated and maintained by GitHub
          JFROG_CLI_BUILD_NAME: jfrog-docker-build-example
          # JFrog organization secret
          JFROG_CLI_BUILD_NUMBER : ${{ github.run_number }}
        run: |
          # Export the build name and build nuber
          # Collect environment variables for the build
          jf rt build-collect-env
          # Collect VCS details from git and add them to the build
          jf rt build-add-git
          # Publish build info
          jf rt build-publish
