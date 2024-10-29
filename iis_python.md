# Python in IIS

1. Install Python

## IIS
1. Go to **Windows feature on or off**
2. Select everything in **Internet Information Service** except **FTP Server**

## Create a new **Web Site**
1. Map the physical path to the python project
2. Add the ```web.config``` to the root directory
3. make sure to fill processPath with the environment python path

==========> https://learn.microsoft.com/en-us/visualstudio/python/configure-web-apps-for-iis-windows?view=vs-2022

```
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <system.webServer>
        <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" requireAccess="Script" />
        </handlers>
        <httpPlatform stdoutLogEnabled="true" stdoutLogFile=".\python.log" startupTimeLimit="20" processPath="YPUR_PYTHON_ENV" arguments="-m uvicorn --port %HTTP_PLATFORM_PORT% main:app">
        </httpPlatform>
    </system.webServer>
</configuration>
```

## Problems
### HttpPlatformHandler 
> Install it (HERE)[https://www.iis.net/downloads/microsoft/httpplatformhandler]

### Application Request Routing
> Install it (HERE)[https://www.iis.net/downloads/microsoft/application-request-routing]

### Permissions
> Fix it (HERE)[https://stackoverflow.com/questions/6176093/http-error-500-0-internal-server-error-an-unknown-fastcgi-error-occured]
