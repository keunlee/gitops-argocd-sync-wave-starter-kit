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

4. Create an ArgoCD Application

# GitOps Workflow - Asynchronous Execution

- click: ***Applications (Stack Icon)::New App***
- fill in the form fields:
    - General 
        - ***Application Name***: beer-horoscope
        - ***Project***: default
        - ***Sync Policy***: Manual (Defaults)
        - ***Sync Options***: None (Defaults)
        - ***Prune Propogation Policy***: foreground
    - Source
        - ***Repository Url***: https://github.com/beer-horoscope/beer-horoscope.git
        - ***Revision***: HEAD
        - ***Path***: gitops
    - Destination
        - ***Cluster Url***: https://kubernetes.default.svc
        - ***Namespace***: beer-rec-system
    - Helm
        - ***Values File***: values.yaml
        - ***spec.destination.server***: https://kubernetes.default.svc
![Screenshot from 2021-06-08 08-28-27](https://user-images.githubusercontent.com/61749/121194029-f7ba4880-c833-11eb-8438-5be1f712fc83.png)

# GitOps Workflow - Synchronous Execution

- click: ***Applications (Stack Icon)::New App***
- fill in the form fields:
    - General 
        - ***Application Name***: beer-horoscope
        - ***Project***: default
        - ***Sync Policy***: Manual (Defaults)
        - ***Sync Options***: None (Defaults)
        - ***Prune Propogation Policy***: foreground
    - Source
        - ***Repository Url***: https://github.com/beer-horoscope/beer-horoscope.git
        - ***Revision***: HEAD
        - ***Path***: gitops
    - Destination
        - ***Cluster Url***: https://kubernetes.default.svc
        - ***Namespace***: beer-rec-system
    - Helm
        - ***Values File***: values.yaml
        - ***spec.destination.server***: https://kubernetes.default.svc
![Screenshot from 2021-06-08 08-28-27](https://user-images.githubusercontent.com/61749/121194029-f7ba4880-c833-11eb-8438-5be1f712fc83.png)
