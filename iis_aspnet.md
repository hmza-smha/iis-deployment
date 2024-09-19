# ASP.NET in IIS

## IIS
1. Go to **Windows feature on or off**
2. Select everything in **Internet Information Service** except **FTP Server**

## ASP.NET 
1. Publish the code with the proper **connection string** to a desired folder
2. Note the database conections strings

## Create a new **Application Pool**
1. Choose **.NET CLR v4** as .NET CLR Version

## Create a new **Web Site**
1. Choose the created pool
2. Map the physical path to the DLL files


## Problem
https://dotnet.microsoft.com/en-us/download/dotnet/thank-you/runtime-aspnetcore-8.0.8-windows-hosting-bundle-installer
