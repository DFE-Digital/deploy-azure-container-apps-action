# Deploy Azure Container Apps GitHub Action

This GitHub Workflow will build a Dockerfile, push it to an Azure Container Registry, and then update an Azure Container App template with the resulting image name

## Usage

Combine all composite actions together for a simple deployment workflow

```yml
  build-import-deploy:
    name: Build, Import, Deploy
    permissions:
      id-token: write
      packages: write
      attestations: write
    runs-on: ubuntu-24.04
    environment: development
    steps:
      - uses: DFE-Digital/deploy-azure-container-apps-action/.github/actions/build@v5.2.0
        with:
          image-name: my-image
          github-token: ${{ secrets.GITHUB_TOKEN }}

      - uses: DFE-Digital/deploy-azure-container-apps-action/.github/actions/import@v5.2.0
        with:
          azure-tenant-id: ${{ secrets.AZURE_TENANT_ID }}
          azure-subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
          azure-acr-client-id: ${{ secrets.ACR_CLIENT_ID }} # uses OIDC auth
          azure-acr-name: ${{ secrets.ACR_NAME }}
          image-name: my-image
          github-token: ${{ secrets.GITHUB_TOKEN }}

      - uses: DFE-Digital/deploy-azure-container-apps-action/.github/actions/deploy@v5.2.0
        with:
          azure-tenant-id: ${{ secrets.AZURE_TENANT_ID }}
          azure-subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
          azure-aca-client-id: ${{ secrets.ACA_CLIENT_ID }}
          azure-aca-name: ${{ secrets.ACA_CONTAINERAPP_NAME }}
          azure-aca-resource-group: ${{ secrets.ACA_RESOURCE_GROUP }}
          azure-acr-name: ${{ secrets.ACR_NAME }}
          annotate-release: 'yes'
          image-name: my-image
```

Or run certain steps in a matrix when dealing with things like worker or init containers

```yml
  build-import:
    name: Build & Import
    runs-on: ubuntu-24.04
    environment: development
    permissions:
      packages: write
      id-token: write
      attestations: write
    strategy:
      matrix:
        stage: [
          "final",
          "initcontainer"
        ]
        include:
          - stage: "final"
            tag-prefix: ""
          - stage: "initcontainer"
            tag-prefix: "init-"
    steps:
      - uses: DFE-Digital/deploy-azure-container-apps-action/.github/actions/build@v5.2.0
        with:
          build-target: ${{ matrix.stage }}
          image-name: my-cool-app
          tag-prefix: ${{ matrix.tag-prefix }}
          github-token: ${{ secrets.GITHUB_TOKEN }}

      - uses: DFE-Digital/deploy-azure-container-apps-action/.github/actions/import@v5.2.0
        with:
          azure-tenant-id: ${{ secrets.AZURE_TENANT_ID }}
          azure-subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
          azure-acr-client-id: ${{ secrets.ACR_CLIENT_ID }}
          azure-acr-name: ${{ secrets.ACR_NAME }}
          image-name: my-cool-app
          tag-prefix: ${{ matrix.tag-prefix }}
          github-token: ${{ secrets.GITHUB_TOKEN }}

  deploy:
    name: Deploy
    needs: [ build-import ]
    runs-on: ubuntu-24.04
    environment: development
    permissions:
      id-token: write
    steps:
      - uses: DFE-Digital/deploy-azure-container-apps-action/.github/actions/deploy@v5.2.0
        with:
          azure-tenant-id: ${{ secrets.AZURE_TENANT_ID }}
          azure-subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
          azure-aca-client-id: ${{ secrets.ACA_CLIENT_ID }}
          azure-aca-name: ${{ secrets.ACA_CONTAINERAPP_NAME }}
          azure-aca-resource-group: ${{ secrets.ACA_RESOURCE_GROUP }}
          azure-acr-name: ${{ secrets.ACR_NAME }}
          annotate-release: 'yes'
          image-name: my-cool-app
```
