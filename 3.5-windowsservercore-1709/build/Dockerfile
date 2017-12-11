FROM microsoft/dotnet-framework-build:3.5-2017.12-windowsservercore-1709

# Install web targets
RUN Invoke-WebRequest https://dotnetbinaries.blob.core.windows.net/dockerassets/MSBuild.Microsoft.VisualStudio.Web.targets.2017.12.zip \
        -UseBasicParsing \
        -OutFile MSBuild.Microsoft.VisualStudio.Web.targets.zip; \
    Expand-Archive MSBuild.Microsoft.VisualStudio.Web.targets.zip \
        -DestinationPath \"${Env:ProgramFiles(x86)}\Microsoft Visual Studio\2017\BuildTools\MSBuild\Microsoft\VisualStudio\v15.0\"; \
    Remove-Item -Force MSBuild.Microsoft.VisualStudio.Web.targets.zip

# Install node, bower, and git
ENV NODE_VERSION 8.9.1
RUN Invoke-WebRequest https://nodejs.org/dist/v${env:NODE_VERSION}/node-v${env:NODE_VERSION}-win-x64.zip -UseBasicParsing -OutFile node.zip; \
    Expand-Archive node.zip -DestinationPath nodejs-tmp; \
    Move-Item nodejs-tmp/node-v${env:NODE_VERSION}-win-x64 ${env:ProgramFiles}\nodejs; \
    Remove-Item -Force node.zip; \
    Remove-Item -Force nodejs-tmp
RUN setx /M PATH $(${Env:PATH} + \"${Env:ProgramFiles}\nodejs\")
RUN & ${env:ProgramFiles}\nodejs\npm install -g gulp bower

# Add ASP.NET runtime components
RUN powershell -Command Add-WindowsFeature Web-Server & \
    powershell -Command Add-WindowsFeature Web-Asp-Net & \
    %windir%\System32\inetsrv\appcmd set apppool /apppool.name:DefaultAppPool /managedRuntimeVersion:v2.0 & \
    powershell -Command Remove-Item -Recurse C:\inetpub\wwwroot\*

ADD ServiceMonitor.exe /

EXPOSE 80
