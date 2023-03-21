<img src="../../../img/logo.png" alt="Chmurowisko logo" width="200" align="right">
<br><br>
<br><br>
<br><br>

# Installing Istio

## LAB Overview

#### In this lab you will install Istio

## Task 1: Istio installation

1. curl -L https://istio.io/downloadIstio | sh -
2. cd istio-1.17.1
3. export PATH=$PWD/bin:$PATH
4. istioctl x precheck
5. istioctl install --set profile=demo
6. istioctl verify-install
7. kubectl get ns
8. kubectl -n istio-system get deploy
9. kubectl get svc istio-ingressgateway -n istio-system
10. Install Prometheus:
    
    ```bash
    kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.16/samples/addons/prometheus.yaml
    ```
11. Install Kiali:
   
    ```bash
    kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.16/samples/addons/kiali.yaml
    ```

12. Install Jeager:
    
    ```bash
    kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.16/samples/addons/jaeger.yaml
    ```


## END LAB

<br><br>

<center><p>&copy; 2023 Chmurowisko Sp. z o.o.<p></center>
