# Angular in IIS

## IIS
1. Go to **Windows feature on or off**
2. Select everything in **Internet Information Service** except **FTP Server**

## Angular
1. Run ```ng build --base-ref ""```

## Create a new **Application Pool**
1. Choose **No Managed Code** as .NET CLR Version
   
## Create a new **Web Site**
1. Map the physical path to the **dist** file

### Problem
1. Make sure you have **URL Rewrite** or download it [HERE](https://www.iis.net/downloads/microsoft/url-rewrite)
2. Add ```web.config``` file in the root of the **dist** file

```
<?xml version="1.0" encoding="utf-8"?>
<configuration>

<system.webServer>
  <rewrite>
    <rules>
      <rule name="Angular Routes" stopProcessing="true">
        <match url=".*" />
        <conditions logicalGrouping="MatchAll">
          <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="true" />
          <add input="{REQUEST_FILENAME}" matchType="IsDirectory" negate="true" />
        </conditions>
        <action type="Rewrite" url="/" />
      </rule>
    </rules>
  </rewrite>
</system.webServer>

</configuration>
```
