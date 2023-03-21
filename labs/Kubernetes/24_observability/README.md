# Observability Prometheus, Grafana, Loki

## Instalacja nginx-ingress

Zanim zainstalujesz nginx-ingress upewnij się, że na Twoim klastrze nie ma zainstalowanych poniższych komponentów:
- ingressclass (obiekt nie-namespace'owalny)
```shell
kubectl get ingressclass nginx
```
- ValidatingWebhookConfiguration (obiekt nie-namespace'owalny)
```shell
kubectl get validatingwebhookconfigurations ingress-nginx-admission
```
Jeśli ww. obiekty istnieją, to należy je usunąć.

Instalacja nginx:
```shell
kubectl create ns nginx-ingress
helm repo add nginx-stable https://helm.nginx.com/stable
helm repo update
helm upgrade --install nginx-ingress nginx-stable/nginx-ingress -n nginx-ingress --set controller.enableLatencyMetrics=true --set prometheus.create=true --set controller.config.name=nginx-local
```

Dostosuj adresy IP w konfiguracji obiektów `Ingress`

```shell
IP=$(kubectl get svc nginx-ingress-nginx-ingress -n nginx-ingress -o json | jq -j '.status.loadBalancer.ingress[].ip')
sed -i "s/IP_TO_REPLACE/$IP/g" grafana/ingress.yaml 

[dla użytkowników maca trzeba dodać parametr -e]
sed -i -e "s/IP_TO_REPLACE/$IP/g" grafana/ingress.yaml
```

## Instalacja Prometheus + Grafana

```shell
kubectl create ns observability
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm upgrade --install prometheus -n observability prometheus-community/kube-prometheus-stack
kubectl apply -f grafana/ingress.yaml
```

Wyświetl stronę Grafana znajdującą się pod adresem:

```shell
kubectl get ingress -n observability -o json | jq '.items | first | .spec.rules | first | .host'
```

Login i hasło znajdziesz za pomocą poniższych komend:

```shell
kubectl get secret -n observability prometheus-grafana -o jsonpath="{.data.admin-user}" | base64 -d
kubectl get secret -n observability prometheus-grafana -o jsonpath="{.data.admin-password}" | base64 -d
```

## Instalacja rabbitmq operator

Instalacja operatora
  
```shell
kubectl apply -f "https://github.com/rabbitmq/cluster-operator/releases/latest/download/cluster-operator.yml"
```

Utworzenie namespace i konfiguracja rabbita
```shell
kubectl apply -f "./rabbitmq/ns.yaml"
kubectl apply -f "./rabbitmq/rabbit.yaml"
kubectl apply -f "./rabbitmq/rabbitmq-servicemonitor.yaml"
kubectl apply -f "./rabbitmq/rabbitmq-operator-podmonitor.yaml"
```

Aktualizacja drivera CSI - problem uprawnień [https://www.linode.com/community/questions/22548/volume-failedmounted]
```shell
kubectl -n kube-system patch sts csi-linode-controller --type='json' -p='[{"op": "add", "path": "/spec/template/spec/containers/0/args/-", "value": "--default-fstype=ext4"}]' 
```

Sprawdź czy instalacja przebiegła poprawnie i poczekaj aż wszystkie pody będą w stanie `Running`
```shell
kubectl get pods -n rabbitmq

```

## Dodanie dashboardu do rabbitmq

Zaloguj się do grafana, przejdź do dashboard i dodaj nowy dashboard poprzez opcję "Import".
Zaimportuj plik `Rabbitmq-overview.json` z folderu `rabbitmq` w tym repozytorium.


## Zaloguj się do konsoli rabbitmq i dodaj wiadomość do kolejki

Pobierz adres konsoli:
```shell
kubectl get svc rabbitmq -n rabbitmq
```

Zaloguj się poprzez przeglądarkę po porcie 15672, login i hasło znajdziesz w pliku `rabbitmq/rabbit.yaml` w tym repozytorium.

## Powróc do dashboardu w grafania i zobacz jak zmieniła się liczba wiadomości w kolejce