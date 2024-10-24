# Publish an ASP.NET Core app to Azure with Visual Studio Code

## Publish
1. Go to project folder
2. Run ```dotnet dev-certs https --trust```
3. Run ```dotnet run```
4. Run ```dotnet publish -c Release -o ./bin/Publish```

## Create a new Azure Web App resource
In the Azure extension tab, in the RESOURCES pane, expand the subscription you wish to use.
Right-click App Services and select Create New Web App....
Follow the prompts:
* Enter a unique name for the web app.
* Select the most recent stable .NET runtime (such as .NET 6 (LTS)). Do not select the ASP.NET runtime, which is for .NET Framework apps.
* Select your pricing tier. Free (F1) is acceptable for this tutorial.

## Publish to Azure
Right click the ```bin\Publish``` folder and select ```Deploy to Web App...``` and follow the prompts.

* Select the subscription where the Azure Web App resource is located.
* Select the Azure Web App resource to which you will publish.
* Select Deploy when prompted with a confirmation dialog.
* Once the deployment is finished, click ```Browse Website``` to validate the deployment.
