# DRLS-REMS-Docker-The Ultimate Guide to Running DRLS REMS locally

## Purpose of this guide

This document details the installation process for the dockerized version of the **Documentation Requirements Lookup Service (DRLS) REMS Workflow** system for Production. There are two approaches to doing this: 

Option 1 utilizes Docker Compose, which comes with Docker Dektop, and requires the corresponding docker-compose.yml file from the REMS repository. This option has minimal technical set up involved and allows for the customization/modification of the dockerized configuration. 

Option 2 utilizes Porter, which requires a seperate installation in addition to Docker Desktop but does not require the use of any local files. This option has the least amount of technical set up involved and is recommended for non-tecnical users of DRLS REMS as it **does not** allow for the customization/modification of the dockerized configuration. 

This document **is designed to take you through the entire set up process for DRLS REMS using docker containers**. It is a standalone guide that does not depend on any supplementary DRLS REMS documentation.

This guide will take you through the development environment setup for each of the following DRLS components:
1. [Coverage Requirements Discovery (CRD)](https://github.com/mcode/CRD)
2. [(Test) EHR FHIR Service](https://github.com/HL7-DaVinci/test-ehr)
3. [Documents, Templates, and Rules (DTR) SMART on FHIR app](https://github.com/mcode/dtr)
4. [Clinical Decision Support (CDS) Library](https://github.com/mcode/CDS-Library)
5. [CRD Request Generator](https://github.com/mcode/crd-request-generator)
6. [REMS](https://github.com/mcode/REMS.git)
7. Keycloak

## Table of Contents
- [Prerequisites](#prerequisites)
- [Install core tools](#install-core-tools)
    * [Installing core tools on MacOS](#installing-core-tools-on-macos)
        + [Install Docker Desktop for Mac](#install-docker-desktop-for-mac)
        + [Install Porter (Option 2 only)](#install-porter-(option-2-only))
- [Clone REMS repository(Option 1 Only)](#clone-rems-repository-(option-1-only))
- [Configure DRLS REMS](#configure-drls-rems)
    * [Add VSAC credentials to environment (Option 1 only)](#add-vsac-credentials-to-environment-(option-1-only))  
    * [Add Compose Project Name to environment (Option 1 only)](#add-compose-project-name-to-environment-(option-1-only))  
- [Run DRLS REMS](#run-drls-rems)
    * [Option 1 - Docker Compose](#option-1---docker-compose)
    * [Option 2 - Porter Install](#option-2---porter-install)
- [Verify DRLS is working](#verify-drls-is-working)


## Prerequisites

Your computer must have these minimum requirements:
- Running MacOS

- x86_64 (64-bit) or equivalent processor
    * Follow these instructions to verify your machine's compliance: https://www.macobserver.com/tips/how-to/mac-32-bit-64-bit/ 
- At least 8 GB of RAM
- At least 256 GB of storage
- Internet access
- [Chrome browser](https://www.google.com/chrome/)
- [Git installed](https://www.atlassian.com/git/tutorials/install-git)

Additionally, you must have credentials (api key) access for the **[Value Set Authority Center (VSAC)](https://vsac.nlm.nih.gov/)**. Later on you will add these credentials to your development environment, as they are required for allowing DRLS to pull down updates to value sets that are housed in VSAC. If you don't already have VSAC credentials, you should [create them using UMLS](https://www.nlm.nih.gov/research/umls/index.html).

## Install core tools

### Installing core tools on MacOS

#### Install Docker Desktop for Mac

1. Download the **stable** version of **[Docker for Mac](https://www.docker.com/products/docker-desktop)** and follow the steps in the installer.
2. Once the installation is complete, you should see a Docker icon on your Mac's menu bar (top of the screen). Click the icon and verify that **Docker Desktop is running.**
3. Configure Docker to have access to enough resources. To do this, open Docker Desktop and select Settings > Resources. 

    The defaults for memory at 2GB and possibly CPU as well are too low to run the entire DRLS REMS workflow. If not enough resources are provided, you may notice containers unexpectedly crashing and stopping. Exact requirements for these resource values will depend on your machine. That said, as a baseline starting point, the system runs relatively smoothly at 16GB memory and 6 CPU Processors on MITRE issued Mac Devices.

#### Install Visual Studio Code and Extensions (Option 1 Only)

The recomended IDE for this set up is Visual Studio Code

1. Install Visual Studio Code - https://code.visualstudio.com
2. Install Docker extension - https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker

#### Install Porter (Option 2 Only)

1. Download and install porter as per https://porter.sh/install/ instructions:
    ```bash
    curl -L https://cdn.porter.sh/latest/install-mac.sh | bash 
    ```
2. Open `.bash_profile` and add the following lines at the very bottom:
    ```bash
    export PATH=$PATH:~/.porter
    ```
4. Save `.bash_profile` and complete the update to `env`: 
    ```bash
    source .bash_profile
    ```
5. Install required Porter plugins 
    ```bash
    porter mixins install docker
    porter mixins install docker-compose
    ```

## Clone REMS repository (Option 1 Only)

1.  clone the REMS repositories from Github:
    ```bash
    git clone https://github.com/mcode/REMS.git REMS  
    ```

    Alternatively, you can download/copy just the docker-compose.yml file from the REMS reposiotry since that is the only file needed for this set up. 

## Open REMS in VSCode (Option 1 Only)

The Docker Extension for VsCode has useful functionality to aid in the development process using this set up guide. This extension lets you eaily visualize the containers, images, networks, and volumes created by this set up. Clicking on a running container will open up the file structure of the container. Right clicking on a running container will give the option to view container logs (useful to see output from select services), attach a shell instance within the container, and attach a Visual Studio Code IDE to the container using remote-containers. See: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker

## Configure DRLS REMS

### Add VSAC credentials to environment (Option 1 only)

> At this point, you should have credentials to access VSAC. If not, please refer to [Prerequisites](#prerequisites) for how to create these credentials and return here after you have confirmed you can access VSAC.
> To download the full ValueSets, your VSAC account will need to be added to the CMS-DRLS author group on https://vsac.nlm.nih.gov/. You will need to request membership access from an admin. If this is not configured, you will get `org.hl7.davinci.endpoint.vsac.errors.VSACValueSetNotFoundException: ValueSet 2.16.840.1.113762.1.4.1219.62 Not Found` errors.

> While this step is optional, we **highly recommend** that you do it so that DRLS will have the ability to dynamically load value sets from VSAC. 

You can see a list of your pre-existing environment variables on your Mac by running `env` in your Terminal. To add to `env`:
1. Set "VSAC_API_KEY" in the .env file in the REMS Repository

or 

1. `cd ~/`
2. Open `.bash_profile` and add the following lines at the very bottom:
    ```bash
    export VSAC_API_KEY=vsac_api_key
    ```
3. Save `.bash_profile` and complete the update to `env`: 
    ```bash
    source .bash_profile
    ```

> Be aware that if you have chosen to skip this step, you will be required to manually provide your VSAC credentials at http://localhost:8090/data and hit **Reload Data** every time you want DRLS to use new or updated value sets.

### Add Compose Project Name to environment (Option 1 only)

Note: The compose project name is to disambiguate between different set ups on the same machine and can be set to any identifier. If you are following both options mentioned in this guide, it is reccomended to change the compose project name for each so that they differ.

You can see a list of your pre-existing environment variables on your Mac by running `env` in your Terminal. To add to `env`:
1. Set "COMPOSE_PROJECT_NAME" as "rems_prod" in the .env file in the REMS Repository 

or 

1. `cd ~/`
2. Open `.bash_profile` and add the following lines at the very bottom:
    ```bash
    export COMPOSE_PROJECT_NAME=rems_prod
    ```
3. Save `.bash_profile` and complete the update to `env`: 
    ```bash
    source .bash_profile
    ```


## Run DRLS REMS
### Option 1 - Docker Compose
#### Start docker compose application 

```bash
    cd REMS # Need to execute commands in directory with corresponding docker-compose.yml file located in the REMS repository 
    docker-compose up 
```

#### Stop docker-compose application
```bash
    docker-compose down # Removes application servers

    docker volume prune # Optional - Removes persisted data
```
#### Updating docker-compose application images

```bash
    docker-compose build --no-cache --pull [<service_name1> <service_name2> ...] 
    docker-compose --force-recreate  [<service_name1> <service_name2> ...]
```

```bash

    # Options:
    #   --force-recreate                        Recreate containers even if their configuration and image haven't changed.
    #   --build                                 Build images before starting containers.
    #   --pull                                  Pull published images before building images.
    #   --no-cache                              Do not use cache when building the image.
    #   [<service_name1> <service_name2> ...]   Services to recreate, not specifying any service will rebuild and recreate all services
```

### Option 2 - Porter Install
#### Install and Run Porter application 

You can set the flag --allow-docker-host-access in the below commands with the PORTER_ALLOW_DOCKER_HOST_ACCESS environment variable so that you don’t have to specify it for every command.

```bash
    porter install fullstack_rems --allow-docker-host-access --reference codexrems/fullstack_rems:latest # Initial Installation needs to be from remote repository

    or 

    porter install fullstack_rems --allow-docker-host-access  # Subsequent runs can use the local installation

```
Note: The project will keep running in the background when you "ctrl + c" out of the above process. To stop running all together, use the stop command below 

#### Stop Running Porter application and Uninstall
```bash
    porter invoke fullstack_rems --action stop --allow-docker-host-access 
```

#### Updating Porter application 

```bash
    porter upgrade fullstack_rems --allow-docker-host-access # Pull and Update application images and recreate containers

    or 

    porter upgrade fullstack_rems --allow-docker-host-access --reference codexrems/fullstack_rems:latest # Pull and Update Invocation Image in addition to applicaion images from remote repository and recreate containers
```

## Verify DRLS is working

### Register the test-ehr

1. Go to http://localhost:3005/register.
    - Client Id: **app-login**
    - Fhir Server (iss): **http://localhost:8080/test-ehr/r4**
2. Click **Submit**

### The fun part: Generate a test request

1. Go to http://localhost:3000/ehr-server/reqgen.
2. Click **Patient Select** button in upper left.
3. Find **William Oster** in the list of patients and click the dropdown menu next to his name.
4. Select **E0470** in the dropdown menu.
5. Click anywhere in the row for William Oster.
6. Click **Submit** at the bottom of the page.
7. After several seconds you should receive a response in the form of two **CDS cards**:
    - **Respiratory Assist Device**
    - **Positive Airway Pressure Device**
8. Select **Order Form** on one of those CDS cards.
9. If you are asked for login credentials, use **alice** for username and **alice** for password.
10. A webpage should open in a new tab, and after a few seconds, a questionnaire should appear.
11. Fill out questionnaire and hit next
<!-- 12. Submit REMS Request to http://localhost:9015/fhir   -->
13. Submit PAS request to https://davinci-prior-auth.logicahealth.org/fhir

Congratulations! DRLS is fully installed and ready for you to use!

## Troubleshooting docker-sync
Reference: https://docker-sync.readthedocs.io/en/latest/troubleshooting/sync-stopping.html
