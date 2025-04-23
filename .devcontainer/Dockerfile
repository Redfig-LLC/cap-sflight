#!/usr/bin/env bash
# See here for image contents: https://github.com/microsoft/vscode-dev-containers/tree/v0.194.0/containers/typescript-node/.devcontainer/base.Dockerfile

# [Choice] Node.js version: 18, 16, 14, 12
ARG VARIANT="20-bookworm"
FROM mcr.microsoft.com/vscode/devcontainers/typescript-node:${VARIANT}

# [Optional] Uncomment this section to install additional OS packages.
RUN wget -q -O - https://packages.cloudfoundry.org/debian/cli.cloudfoundry.org.key | sudo apt-key add - \
    && echo "deb https://packages.cloudfoundry.org/debian stable main" | sudo tee /etc/apt/sources.list.d/cloudfoundry-cli.list
RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends cf8-cli \
    && apt install -y libnss3 xvfb \ 
    && sudo apt-get install -y libatk-bridge2.0-0 libgtk-3.0 libasound2 libgbm-dev

# RUN cf add-plugin-repo CF-Community https://plugins.cloudfoundry.org \
#     && cf install-plugin multiapps -f && cf install-plugin html5-plugin -f
# ARG MULTIAPPS_ARCH="linuxarm64"
ARG MULTIAPPS_VERSION="3.5.0"
# ARG MBT_ARCH="arm64"
ARG MBT_VERSION="1.2.34"
# RUN if [ '$(arch)' = 'x86_64' ]; then \
#     MULTIAPPS_ARCH="linux64" && MBT_ARCH="amd64"; \
#     fi
RUN cf add-plugin-repo CF-Community https://plugins.cloudfoundry.org 
RUN if [ '$(arch)' = 'x86_64' ]; then \
    cf install-plugin html5-plugin -f; \
    fi 
# Multiapps plugin doesn't work without being logged in to user I think.
# This is the MTA Build Tool (mbt); putting here for good measure
RUN MBT_ARCH=$(arch | sed s/aarch64/arm64/ | sed s/x86_64/amd64/) &&\
    wget -q https://github.com/SAP/cloud-mta-build-tool/releases/download/v${MBT_VERSION}/cloud-mta-build-tool_${MBT_VERSION}_Linux_${MBT_ARCH}.tar.gz \
    && tar -xzf cloud-mta-build-tool_${MBT_VERSION}_Linux_${MBT_ARCH}.tar.gz -C /usr/local/bin \
    && rm cloud-mta-build-tool_${MBT_VERSION}_Linux_${MBT_ARCH}.tar.gz

ARG CDS_DK_VERSION="latest"
ARG UI5_CLI_VERSION="3"
ARG CF_USERNAME
ARG CF_PASSWORD
ARG GIT_NAME
ARG GIT_EMAIL
ARG CF_REGION="us10-001"

# [Optional] Uncomment if you want to install more global node packages
# RUN su node -c "npm install -g yo mbt @sap/cds-dk@${CDS_DK_VERSION} @ui5/cli@${UI5_CLI_VERSION} && git config --global core.autocrlf true \
RUN MULTIAPPS_ARCH=$(arch | sed s/aarch64/linuxarm64/ | sed s/x86_64/linux64/) &&\ 
    su node -c "npm install -g yo && git config --global core.autocrlf true \
    && cf api https://api.cf.${CF_REGION}.hana.ondemand.com \
    && cf install-plugin https://github.com/cloudfoundry/multiapps-cli-plugin/releases/download/v${MULTIAPPS_VERSION}/multiapps-plugin.${MULTIAPPS_ARCH} -f \
    && git config --global user.email '${GIT_EMAIL}' && git config --global user.name '${GIT_NAME}'"
