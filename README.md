# Overview

A brief overview of what and how to use ArgoCD's Sync and Wave feature to orchestrate the sequence of execution in your GitOps workflows. 

# Pre-requisites

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

- click on the application tile **01-without-sync-wave** you'll be met with the following screen: 
![Screenshot from 2021-07-02 16-44-39](https://user-images.githubusercontent.com/61749/124331474-df98c900-db54-11eb-8ee3-b85984199de8.png)
- click: **Sync::Synchronize** and observe the deployment of the pods

Notice that all the pods are deployed asynchronously

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

- click on the application tile **01-without-sync-wave** you'll be met with the following screen: 
![Screenshot from 2021-07-02 16-53-04](https://user-images.githubusercontent.com/61749/124331977-fdb2f900-db55-11eb-9195-8147e678d158.png)
- click: **Sync::Synchronize** and observe the deployment of the pods

Notice the synchronous order that the pods are deployed in. 

### Presync

A Presync will execute before waves are executed. It is the first step in a workflow to run. To leverage a workflow step as a Presync add the following annotations: 

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pre-sync
  # PreSync Annotations
  annotations:
    argocd.argoproj.io/hook: PreSync
    argocd.argoproj.io/hook-delete-policy: HookSucceeded
spec:
  containers:
  - name: pre-sync
    image: busybox:latest
    command: ["/bin/sh", "-c", "tail -f /dev/null"]
```

### Postsync

A Postsync will execute after all waves are executed. It is the last step in a workflow to run. To leverage a workflow step as a Postsync add the following annotations: 

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: post-sync
  # PostSync Annotations
  annotations:
    argocd.argoproj.io/hook: PostSync
    argocd.argoproj.io/hook-delete-policy: HookSucceeded
spec:
  containers:
  - name: post-sync
    image: busybox:latest
    command: ["/bin/sh", "-c", "tail -f /dev/null"]
```

### Waves

Waves are determine when a workflow step runs. An starting order of 0 and through N denotes the order that a wave is run. Waves can be run in parallel and/or in sequence depending on how a workflow step's wave is numbered. To assign a workflow step to a wave add the following annotations: 

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: group-01-step-01a
  annotations:
    argocd.argoproj.io/sync-wave: "1"
spec:
  containers:
  - name: group-01-step-01a
    image: busybox:latest
    command: ["/bin/sh", "-c", "tail -f /dev/null"]
---
apiVersion: v1
kind: Pod
metadata:
  name: group-01-step-01b
  annotations:
    argocd.argoproj.io/sync-wave: "1"
spec:
  containers:
  - name: group-01-step-01b
    image: busybox:latest
    command: ["/bin/sh", "-c", "tail -f /dev/null"]
---
apiVersion: v1
kind: Pod
metadata:
  name: group-02-step-01
  annotations:
    argocd.argoproj.io/sync-wave: "2"
spec:
  containers:
  - name:  group-02-step-01
    image: busybox:latest
    command: ["/bin/sh", "-c", "tail -f /dev/null"]
```

The steps above will execute in the following order: 

- **Sequence 1:** In Parallel - `group-01-step-01a` and `group-01-step-01b`
- **Sequence 2:** `group-02-step-01`

# Troubleshooting/Issues

If you've just installed the GitOps Operator on your cluster for the first time are getting permission related errors/messages, run the following to assign the cluster admin role to the service account `openshift-gitops-argocd-application-controller`

```bash
oc adm policy add-cluster-role-to-user cluster-admin -z openshift-gitops-argocd-application-controller -n openshift-gitops
```

