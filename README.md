# IBM Client Developer Advocacy App Modernization Series

## Lab - Creating your own Helm repositories and deploying to IBM Cloud Private 

## Overview 

Helm is a package manager. Package managers automate the process of installing, configuring, upgrading, and removing computer programs. Examples include the Red Hat Package Manager (RPM), Homebrew, and Windows® PackageManagement.

An application in Kubernetes typically consists of at least two resource types: a deployment resource, which describes a set of pods to be deployed together, and a services resource, which defines endpoints for accessing the APIs in those pods. The application can also include ConfigMaps, Secrets, and Ingress.

For any deployment, you need several Kubernetes commands (kubectl) to create and configure resources. Instead of manually creating each resource separately, you can create many resources with one command grealtly simplifying the process and allowing you to manage the releted resources as a single unit called a chart. 

A Helm chart repository is an HTTP server that houses packaged charts and an index.yaml file. That file has an index of all the charts in the repository. A chart repository can be any HTTP server that can serve YAML and .tar files and can answer GET requests. Therefore, you have many options for hosting your chart repository. You can use a Google Cloud Storage bucket, an Amazon S3 bucket, GitHub pages, or you can create a web server.

In this lab you'll create a Helm char reposiotry and use it to deploy a smple JEE app to IBM Cloud Private using the Helm CLI

### Step 1: Clone the Github repo that contains the code and templates  used to create the Helm repository 

1. Login in [your Github account](https://github.com)

2. 
