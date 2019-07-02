FROM mcr.microsoft.com/windows/servercore:ltsc2019 as download

ENV RUBY_VERSION 2.6.3.1
ENV INSPEC_VERSION 3.9.3

SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop'; $ProgressPreference = 'SilentlyContinue';"]

ENV chocolateyUseWindowsCompression=false

RUN iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1')) ; \
    choco feature disable --name showDownloadProgress ; \
    choco install -y ruby --version $env:RUBY_VERSION

# http://guides.rubygems.org/ssl-certificate-update/#installing-using-update-packages
RUN $AllProtocols = [System.Net.SecurityProtocolType]'Ssl3,Tls,Tls11,Tls12'; \
    [System.Net.ServicePointManager]::SecurityProtocol = $AllProtocols; \
    gem install inspec --no-document -v $env:INSPEC_VERSION

RUN rm /tools/ruby26/unins*

FROM mcr.microsoft.com/windows/servercore:ltsc2019

COPY --from=download /tools/ruby26 /tools/ruby26
COPY --from=download /programdata/chocolatey /programdata/chocolatey

RUN setx /M PATH "C:\tools\ruby26\bin;%PATH%";"C:\programdata\chocolatey\bin;%PATH%"
