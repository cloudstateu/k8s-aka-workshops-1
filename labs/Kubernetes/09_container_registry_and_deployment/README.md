<img src="./img/logo.png" alt="Chmurowisko logo" width="200" align="right">
<br><br>
<br><br>
<br><br>

# Container Registry and Deployment

## Lab Overview

In this lab you will use DockerHub as a container registry. You'll configure Kubernetes to pull images from the registry and run them on a cluster.

## Task 1: Run application locally

1. Go to `/app` directory and install application dependencies

    ```bash
    npm i
    ```

1. Start the application:

    ```bash
    npm start
    ```

1. Using second Cloud Shell session, verify the application is responding to requests:

    ```bash
    curl localhost:8081
    ```

1. Stop the application using `Ctrl-C`

## Task 2: Create Kubernetes secret with ACR credentials

1. Create Kubernetes Secret using command below. Make sure to provide credentials for your account.

    ```bash
    kubectl create secret docker-registry dockerregistrycredential \
        --docker-server=docker.io \
        --docker-username=jramutaks \
        --docker-password=akamai1234 \
        --docker-email=jakub.ramut@chmurowisko.pl
    ```

1. Verify that secret is created

    ```bash
    kubectl get secret
    ```

## Task 3: Create Deployment and Service on Kubernetes

1. Open [files/cm-acr-app.yaml](./files/cm-acr-app.yaml) and verify its contents
1. Update the `.spec.template` section:

    ```yaml
    template:
      metadata:
        labels:
          app: cm-acr-app
      spec:
        containers:
        - name: app
          image: jramutaks/cm-acr-app:latest #point to the correct image
          ports:
          - containerPort: 8080
        imagePullSecrets:
        - name: dockerregistrycredential
    ```

1. Apply the updated file to Kubernetes:

    ```bash
    kubectl apply -f cm-acr-app.yaml
    ```

1. Get IP for cm-acr-app Service and visit it in browser.

    ```bash
    kubectl get svc
    ```

## Task 4: Delete resources created during this lab

1. Delete Kubernetes resources

    ```bash
    kubectl delete -f cm-acr-app.yaml
    ```

## END LAB

<br><br>

<center><p>&copy; 2023 Chmurowisko Sp. z o.o.<p></center>
