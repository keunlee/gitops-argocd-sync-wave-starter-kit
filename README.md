# Overview

A brief overview of what and how to use ArgoCD's Sync and Wave feature to orchestrate the sequence of execution in your GitOps workflows. 

# Prequisites

- Red Hat Openshift (4.7.x)
- Red Hat Openshift GitOps Operator (1.1.2)
- `oc` CLI application

# ArgoCD Setup

1. Fork this repository

2. Add your forked repository into ArgoCD
- Click **Gears Icon::Repositories::Connect Repo Using Https**
- Fill in the **Repository URL** field
- Click **Connect**

    ![Screenshot from 2021-07-02 14-39-36](https://user-images.githubusercontent.com/61749/124321491-6775d780-db43-11eb-95b3-258420bb7e56.png)

3. Create an ArgoCD Project
- Click **Gears Icon::Projects::New Project**
- Fill in the form fields **Name** and **Description**
    - Name: argocd-sync-wave-demo
    - Descriptiion: Demonstration of ArgoCD Syncs and Waves

    ![Screenshot from 2021-07-02 15-01-08](https://user-images.githubusercontent.com/61749/124323124-62feee00-db46-11eb-9145-5025c371d297.png)
- Click **Create**
- Click on the project from the list and edit the project so that it looks similar to the following: 
![Screenshot from 2021-07-02 15-47-59](https://user-images.githubusercontent.com/61749/124326939-eb808d00-db4c-11eb-9fb0-016f6fab014c.png)

4. Create an ArgoCD Application

# GitOps Workflow - Asynchronous Execution

## Create Application

- click: ***Applications (Stack Icon)::New App***
- fill in the form fields:
    - General 
        - ***Application Name***: 01-without-sync-wave
        - ***Project***: default
        - ***Sync Policy***: Manual (Defaults)
        - ***Sync Options***:
            - ***check***: Auto-create Namespace
        - ***Prune Propogation Policy***: foreground
    - Source
        - ***Repository Url***: https://github.com/keunlee/gitops-argocd-syncs-waves.git
        - ***Revision***: HEAD
        - ***Path***: 01-without-sync-wave
    - Destination
        - ***Cluster Url***: https://kubernetes.default.svc
        - ***Namespace***: 01-without-sync-wave
    - Kustomize
        - leave all values at default
- click ***Create***
![Screenshot from 2021-07-02 15-42-20](https://user-images.githubusercontent.com/61749/124326964-fc310300-db4c-11eb-816c-6021905e6f87.png)

## Synchronize and Observe

# GitOps Workflow - Synchronous Execution

## Create Application

- click: ***Applications (Stack Icon)::New App***
- fill in the form fields:
    - General 
        - ***Application Name***: 02-with-sync-wave
        - ***Project***: default
        - ***Sync Policy***: Manual (Defaults)
        - ***Sync Options***:
            - ***check***: Auto-create Namespace
        - ***Prune Propogation Policy***: foreground
    - Source
        - ***Repository Url***: https://github.com/keunlee/gitops-argocd-syncs-waves.git
        - ***Revision***: HEAD
        - ***Path***: 02-with-sync-wave
    - Destination
        - ***Cluster Url***: https://kubernetes.default.svc
        - ***Namespace***: 02-with-sync-wave
    - Kustomize
        - leave all values at default
- click ***Create***
![Screenshot from 2021-07-02 15-58-07](https://user-images.githubusercontent.com/61749/124327984-64341900-db4e-11eb-96b7-14086cf672c9.png)

## Synchronize and Observe