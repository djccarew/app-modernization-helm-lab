# IBM Client Developer Advocacy App Modernization Series

## Lab - Migrating Legacy JEE apps to IBM Cloud Private

### Part 2 - Working with Helm

## Overview

Helm is a package manager for Kubernetes. Package managers automate the process of installing, configuring, upgrading, and removing computer programs. Examples include the Red Hat Package Manager (RPM), Homebrew, and Windows® PackageManagement.

An application in Kubernetes typically consists of at least two resource types: a deployment resource, which describes a set of pods to be deployed together, and a services resource, which defines endpoints for accessing the APIs in those pods. The application can also include ConfigMaps, Secrets, and Ingress.

For any deployment, you need several Kubernetes commands (kubectl) to create and configure resources. Instead of manually creating each resource separately, you can create many resources with one command greatly simplifying the process and allowing you to manage the related resources as a single unit called a chart.

A Helm chart repository is an HTTP server that houses packaged charts and an index.yaml file. That file has an index of all the charts in the repository. A chart repository can be any HTTP server that can serve YAML and .tar files and can answer GET requests. Therefore, you have many options for hosting your chart repository. You can use a Google Cloud Storage bucket, an Amazon S3 bucket, GitHub pages, or you can create a web server.

In this lab you'll create a Helm chart repository and use it to deploy a small  JEE app to IBM Cloud Private using the Helm CLI

### Setup

If you haven't already:

1. Complete *Part 1 -  Working with IBM Cloud Transformation Advisor*  by following the instructions [here](https://github.com/djccarew/app-modernization-ta-lab)

2. Login to the VM designated as the client env to ICP using the credentials  provided  to you

3. From a  client  terminal window log in to the ICP Cluster with the following command:
```
    cloudctl login -a https://[ICP Master IP]:8443 --skip-ssl-validation
```
4. Go to the folder where you cloned the Plants By WebSphere  app in the previous lab
```
   cd app-modernization-plants-by-websphere-jee6
```

### Step 1: Look at the structure of the Helm chart for the Plants By WebSphere app

1. Login in [your Github account](https://github.com)

2. Select your fork  of the repository **app-modernization-plants-by-websphere-jee6** that you forked in the previous lab.

3. Using the Github's UI  file browser to  take a look at the files in the **chart** folder. This is a Helm chart with child charts for the web app and MariaDB  portions of the app. Since there already is a published chart for  MariaDB, it is listed  as a required child chart in the file **requirements.yaml** and you don't have to create a chart for the MariaDB portion of the app.

### Step 2: Create the artifacts for the Helm repository

**Note:** This lab is designed for a multiuser IBM Cloud Private installation where each student is using a client shell with a unique username available via the ENV var $USER.

1. From your client terminal  type in (or copy and paste in) the following (uncommented) commands
```
   # Initialize helm user profile with a one-time action
   helm init --client-only

   # Fetch required MariaDB chart
   helm repo add ibmcom https://raw.githubusercontent.com/IBM/charts/master/repo/stable
   helm dependency update chart/pbw-liberty-mariadb

   # Generate the chart archive.
   helm package chart/pbw-liberty-mariadb -d docs/charts

   # Generate index for repository
   # substitute your github username for [ghuname]
   helm repo index docs/charts --url https://[ghuname].github.io/app-modernization-plants-by-websphere-jee6/charts

```

2. From the command line type in (or copy and paste in) the following (uncommented) commands to push your changes to Github
```
   # Configure your git profile on the system
   git config --global user.email "[your-github-account-email]"
   git config --global user.name "[your github username]"

   # Flag changes to be pushed
   git add .

   # Commit changes
   git commit -m 'Generated Helm repo for sample app'

   # Push to remote Github repo (you may be prompted for your Github credentials)
   git push -u origin master
```

### Step 3: Configure Github to serve up the repo via HTTP/HTTPS

1. In the Settings for your repo in the *GitHub Pages* section select the *master branch /docs folder* for GitHub Pages and click **Save**.

![Github Pages](images/ss2.png)

2. In your terminal window type the following command, substituting your github username for *[ghuname]*. Verify that the contents of *index.yaml* are returned
```
   curl https://[ghuname].github.io/app-modernization-plants-by-websphere-jee6/charts/index.yaml
```

### Step 4: Add your repo to you list of Helm repos

1. In your terminal window type the following command, substituting your logged in  username for $USER as the repo name  and your github username for [ghuname]  
```
   # Substitute your github username for [ghuname]
   helm repo add $USER-repo https://[ghuname].github.io/app-modernization-plants-by-websphere-jee6/charts
```

### Step 5: Deploy the legacy JEE app from your new Helm repo

1. In your terminal window type the following command, substituting your logged in  username for $USER and your ICP namespace for [yournamespace].  **Note**: Helm charts can be deployed multiple  times but each deployment must have a unique name
```
   helm install --name pbw-liberty-mariadb-$USER --set liberty.image.namespace=[yournamespace] $USER-repo/pbw-liberty-mariadb --tls
```

### Step 6: Launch your deployed app

You can run commands to get the endpoint and port number of your deployed Helm release but it's easy to get a clickable link to the app's endpoint  info from the  IBM Cloud Private Web UI.

1. Launch the IBM Cloud Private Web UI using the URL given to you by your instructor and login in.

2. In the Navigation area on the left expand **Workloads** and select **Helm Releases**

3. Look for your Helm Release in the list and click on the **Launch** link on the right

4. Verify that the app's UI opens in another tab. Click on the **HELP** link.

![Running app](images/ss4.png)

5. Click on **Reset database** to populate the MariaDB database with data

6. Verify that browsing different sections of the online catalog shows product descriptions and images.

![Online catalog](images/ss5.png)

## Summary

With even small simple apps requiring multiple Kubernetes objects,  Helm charts greatly simplify the process of distributing and updating your Kubernetes based apps. Helm repos allow you to distribute your Helm charts via HTTP, further simplifying the process of distributing and deploying your apps.
