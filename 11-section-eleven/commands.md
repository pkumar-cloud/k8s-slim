# Install Helm Mac

```bash
Install Homebrew: /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
brew install helm
helm version
```

# Create & Install chart:

```bash
cd grade-submission-api
helm template . #dry run chart. inject values from values.yaml file into the templates.
helm package . #creates package with "name + version" from Chart.yaml. Whenever update helm chart, change the version and when run again will create 2nd package
helm install <release-name> <package-name> -n <NS-name> #if NS not specified, release will be deployed in "default" ns but artifacts in great-submission
OR
helm install <release-name> . -n <NS-name> #autometically package behind the scene and deploy the release
helm install grade-submission-api grade-submission-api-1.0.1.tgz -n grade-submission
OR
helm install grade-submission-api . -n grade-submission
kubectl get po, svc, secret, cm -n grade-submission-api
helm uninstall grade-submission-api
helm list -A #list all of the helm releases in our cluster
helm upgrade <release-name> <package-name-new> -n <NS-name>
helm rollback <release-name> <revision#> #Rollback to respective revision
```

# Install Helm Windows: Using Chocolatey OR Manual Installation

## Option 1: Using Chocolatey (Recommended)

If you don't have Chocolatey installed, install it first. Open PowerShell as Administrator and run:

```bash
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1')) 2. Once Chocolatey is installed, use it to install Helm: choco install kubernetes-helm
```

## Option 2: Manual Installation

Visit the official Helm GitHub releases page: https://github.com/helm/helm/releases
Download the Windows amd64 version (e.g., helm-v3.x.x-windows-amd64.zip).
Extract the zip file to a directory of your choice (e.g., C:\helm).

Add the directory containing helm.exe to your PATH environment variable:
Right-click on 'This PC' or 'My Computer' and select 'Properties'
Click on 'Advanced system settings'
Click on 'Environment Variables'
Under 'System variables', find and select 'Path', then click 'Edit'
Click 'New' and add the directory path (e.g., C:\helm)
Click 'OK' to close all windows

Note: The directory you add to the PATH should contain the helm.exe file. This executable is what allows you to run Helm commands from any location in the command prompt or PowerShell.
