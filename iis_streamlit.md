# Streamlit in IIS

## Create a new Web Site in IIS
1. Map the physical path to the streamlit project

## web.config
1. Add the following:
```
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <rule name="ReverseProxy" stopProcessing="true">
          <match url="(.*)" />
          <conditions logicalGrouping="MatchAll" trackAllCaptures="false" />
          <action type="Rewrite" url="http://localhost:8501/{R:1}" />
        </rule>
      </rules>
    </rewrite>
    <proxy enabled="true" preserveHostHeader="true" />
  </system.webServer>
</configuration>
```
