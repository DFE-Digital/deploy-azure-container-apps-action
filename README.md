# Deploy Azure Container Apps GitHub Action

This GitHub Workflow will build a Dockerfile, push it to an Azure Container Registry, and then update an Azure Container App template with the resulting image name

## Usage

```yml
jobs:
  build-push-deploy:
    uses: DFE-Digital/deploy-azure-container-apps-action/.github/workflows/build-push-deploy.yml@main
    with:
      docker-image-name: 'my-app'
      docker-build-file-name: 'Dockerfile'
      docker-build-context: '.'
      docker-build-args: 'ENV=development'
      environment: development
      annotate-release: true # defaults to false
    secrets:
      azure-acr-client-id: ${{ secrets.AZURE_ACR_CLIENTID }}
      azure-acr-secret: ${{ secrets.AZURE_ACR_SECRET }}
      azure-acr-url: ${{ secrets.AZURE_ACR_URL }}
      azure-aca-credentials: ${{ secrets.AZURE_ACA_CREDENTIALS }}
      azure-aca-name: ${{ secrets.AZURE_ACA_NAME }}
      azure-aca-resource-group: ${{ secrets.AZURE_ACA_RESOURCE_GROUP }}
```
