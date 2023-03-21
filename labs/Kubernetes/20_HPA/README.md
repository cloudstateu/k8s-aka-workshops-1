<img src="../../../img/logo.png" alt="Chmurowisko logo" width="200" align="right">
<br><br>
<br><br>
<br><br>

# Horizontal Pod Autoscaling

## LAB Overview

**IMPORTANT**
[If you've already installed the metrics-server, you can skip this step]

To see how much resources (CPU, RAM) you are using, you need to install metrics-server. Now in case of verifying some metrics you should see `error: Metrics API not available`. 

metric-server runs a single Pod, that fetch metrics from all our Nodes. 

To install metric-server on your cluster run the following command:
```bash
kubectl apply -f metrics-server.yaml
```

#### In this lab you will work with Horizontal Pod Autoscaling

1. Create deployment from a file`01_deployment.yaml`:
    ```bash
    kubectl apply -f 01_deployment.yaml
    ```

2. In a new / different window set the preview in continuous mode of changes in POD:
    ```bash
    kubectl get pods -w
    ```

3. Create HPA for our deployment:
    ```bash
    kubectl autoscale deployment web --min=1  --max=4 --cpu-percent=30
    ```  

4. Artificial CPU load simulation:
 - Login to POD from Point 1
    ```bash
    kubectl exec -it <Pod_name> -- /bin/bash
    ```

 - Package update:
    ```bash
    apt-get update
    ```

- Installation of the load-generating application:
    ```bash
    apt-get install stress
    ```

 - Starting load generation:
    ```bash
    stress -c 5
    ```

6. In a new / different window we monitor changes in our HPA:
    ```bash
    kubectl get hpa -w
    ```

## END LAB

<br><br>

<center><p>&copy; 2023 Chmurowisko Sp. z o.o.<p></center>