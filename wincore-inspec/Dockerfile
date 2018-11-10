FROM microsoft/windowsservercore:latest as download

ENV RUBY_VERSION 2.5.3.1
ENV RUBYGEMS_VERSION 2.7.7
ENV BUNDLER_VERSION 1.16.1

SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop'; $ProgressPreference = 'SilentlyContinue';"]

ENV chocolateyUseWindowsCompression=false

RUN iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1')) ; \
    choco feature disable --name showDownloadProgress ; \
    choco install -y ruby --version $env:RUBY_VERSION

# http://guides.rubygems.org/ssl-certificate-update/#installing-using-update-packages
RUN $AllProtocols = [System.Net.SecurityProtocolType]'Ssl3,Tls,Tls11,Tls12'; \
    [System.Net.ServicePointManager]::SecurityProtocol = $AllProtocols; \
    (New-Object System.Net.WebClient).DownloadFile('https://rubygems.org/downloads/rubygems-update-{0}.gem' -f $env:RUBYGEMS_VERSION,'C:\rubygems-update.gem') ; \
    gem install --local C:\rubygems-update.gem ; \
    update_rubygems --no-ri --no-rdoc ; \
    gem uninstall rubygems-update -x ; \
    gem install bundler --version $env:BUNDLER_VERSION --force; \
    gem install inspec --no-ri --no-rdoc

RUN rm /tools/ruby25/unins*

FROM microsoft/windowsservercore:latest

COPY --from=download /tools/ruby25 /tools/ruby25
COPY --from=download /programdata/chocolatey /programdata/chocolatey

RUN setx /M PATH "C:\tools\ruby25\bin;%PATH%";"C:\programdata\chocolatey\bin;%PATH%"
