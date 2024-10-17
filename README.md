# Deploy Azure Container Apps GitHub Action

This GitHub Workflow will build a Dockerfile, push it to an Azure Container Registry, and then update an Azure Container App template with the resulting image name

## Usage

* OIDC authorisation (preferred)

```yml
permissions:
  id-token: write # Require write permission to Fetch an OIDC token.
  contents: read  # Require read permission to read the contents of the repository
  packages: write # Require write permission to push the image to GHCR

jobs:
  build-push-deploy:
    uses: DFE-Digital/deploy-azure-container-apps-action/.github/workflows/build-push-deploy.yml@v3.0.0
    with:
      docker-image-name: 'my-app'
      docker-build-file-name: 'Dockerfile'
      docker-build-context: '.'
      docker-build-args: 'ENV=development'
      environment: development
      annotate-release: true # defaults to false
    secrets:
      azure-tenant-id: ${{ secrets.AZURE_TENANT_ID }}
      azure-subscription-id: ${{ secrets.AZURE_SUBSRIPTION_ID }}
      azure-acr-client-id: ${{ secrets.AZURE_ACR_CLIENTID }}
      azure-acr-name: ${{ secrets.AZURE_ACR_NAME }}
      azure-aca-client-id: ${{ secrets.AZURE_ACA_CLIENTID }}
      azure-aca-name: ${{ secrets.AZURE_ACA_NAME }}
      azure-aca-resource-group: ${{ secrets.AZURE_ACA_RESOURCE_GROUP }}
```

* Credential based authorisation

```yml
jobs:
  build-push-deploy:
    uses: DFE-Digital/deploy-azure-container-apps-action/.github/workflows/build-push-deploy.yml@v3.0.0
    with:
      docker-image-name: 'my-app'
      docker-build-file-name: 'Dockerfile'
      docker-build-context: '.'
      docker-build-args: 'ENV=development'
      environment: development
      annotate-release: true # defaults to false
    secrets:
      azure-acr-credentials: ${{ secrets.AZURE_ACR_CREDENTIALS }}
      azure-acr-client-id: ${{ secrets.AZURE_ACR_CLIENTID }}
      azure-acr-name: ${{ secrets.AZURE_ACR_NAME }}
      azure-aca-credentials: ${{ secrets.AZURE_ACA_CREDENTIALS }}
      azure-aca-client-id: ${{ secrets.AZURE_ACA_CLIENTID }}
      azure-aca-name: ${{ secrets.AZURE_ACA_NAME }}
      azure-aca-resource-group: ${{ secrets.AZURE_ACA_RESOURCE_GROUP }}
```
