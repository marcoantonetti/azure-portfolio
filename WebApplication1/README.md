How to publish this ASP.CORE .NET 9 webapp

To AZURE Cloud - you need to have an account and suscription

Using Visual Studio

Right click on web project
Click Publish
Target: Azure - Publish your application to the Microsoft Cloud
Specific Target: Azure App Service (Windows)
Sign in to your Azure Suscription, choose/create resource group and hosting plan
Finish

Using CLI

Open CLI to root folder
Install dotnet if you dont have it
Commands:
 --> dotnet publish -o publish
 --> cd .\publish\
 --> Compress-Archive . publish.zip   
If compress-archive is not installed then you can just go to publish\ and compress it into a .ZIP
 az login 
 --> login to Azure suscription
 --> az group create --location westus --resource-group az-group-rg
 --> az appservice plan create -g az-group-rg -n az-marco-portfolio
Now that the resource group and app service with a plan has been created, we can deploy our .NET web app into Azure Web App
 --> az webapp deployment source config-zip --src .\publish.zip -n az-marco-portfolio -g az-group-rg
 make sure you close file explorer for the last command

Using Docker

Using Continuous Delivery with an automated github deployment on Azure

Copy and paste the following .yaml file or via Azures GUI on deployment section you can set it up automatically

name: Build and deploy ASP.Net Core app to Azure Web App - portfolio-marco-ghub
on:
  push:
    branches:
      - master
  workflow_dispatch:

jobs:
  build:
    runs-on: windows-latest
    permissions:
      contents: read #This is required for actions/checkout

    steps:
      - uses: actions/checkout@v4

      - name: Set up .NET Core
        uses: actions/setup-dotnet@v4
        with:
          dotnet-version: '9.x'

      - name: Build with dotnet
        run: dotnet build --configuration Release

      - name: dotnet publish
        run: dotnet publish -c Release -o "${{env.DOTNET_ROOT}}/myapp"

      - name: Upload artifact for deployment job
        uses: actions/upload-artifact@v4
        with:
          name: .net-app
          path: ${{env.DOTNET_ROOT}}/myapp

  deploy:
    runs-on: windows-latest
    needs: build
    permissions:
      id-token: write #This is required for requesting the JWT
      contents: read #This is required for actions/checkout

    steps:
      - name: Download artifact from build job
        uses: actions/download-artifact@v4
        with:
          name: .net-app
      
      - name: Login to Azure
        uses: azure/login@v2
        with:
          client-id: ${{ secrets.__clientidsecretname__ }}
          tenant-id: ${{ secrets.__tenantidsecretname__ }}
          subscription-id: ${{ secrets.__subscriptionidsecretname__ }}

      - name: Deploy to Azure Web App
        id: deploy-to-webapp
        uses: azure/webapps-deploy@v3
        with:
          app-name: 'portfolio-marco-ghub'
          slot-name: 'Production'
          package: .
          