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

Using Continuous Delivery with an automated githubdeployment 