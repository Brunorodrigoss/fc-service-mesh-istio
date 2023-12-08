https://kiali.io/
https://github.com/devfullcycle/fc-istio

Install k3d:
https://k3d.io/v5.6.0/#installation
brew install k3d

Creating Cluster: k3d cluster create mycluster -p "8000:30000@loadbalancer" --agents 2

kubectl config use-context k3d-mycluster

Install Istio:
    https://istio.io/latest/docs/setup/getting-started/
    Move the istio folder to /opt and add the opt/istio/bin into the $PATH

Install istio into the cluster:
    https://istio.io/latest/docs/setup/getting-started/
    https://istio.io/latest/docs/setup/additional-setup/config-profiles/

    istioctl install -y
    kubectl get pods
    kubectl get namespaces
    kubectl get pods -n istio-system

Injecting Sidecar proxy:
    kubectl label namespace default istio-injection=enabled
    kubectl describe namespaces default
    kubectl get pods
    kubectl delete deployments.apps nginx

Configuring Addons:
    https://istio.io/latest/docs/setup/getting-started/#dashboard
    https://istio.io/latest/docs/ops/integrations/prometheus/
    https://istio.io/latest/docs/ops/integrations/kiali/
    https://istio.io/latest/docs/ops/integrations/jaeger/
    https://istio.io/latest/docs/ops/integrations/grafana/

    kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.19/samples/addons/prometheus.yaml
    kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.19/samples/addons/kiali.yaml
    kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.19/samples/addons/jaeger.yaml
    kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.19/samples/addons/grafana.yaml
    kubectl get pods -n istio-system

    https://istio.io/latest/docs/tasks/observability/kiali/
    istioctl dashboard kiali

Concepts:
    Ingress Gateway -> Virtual Service -> Destination Rules -> Workloads




 while true; do curl http://localhost:8000; echo; sleep 0.5; done;


 kiali -> Service -> Actions -> Trafic Shifting

 Test using fortio: https://istio.io/latest/docs/tasks/traffic-management/circuit-breaking/#adding-a-client
    kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.19/samples/httpbin/sample-client/fortio-deploy.yaml
    export FORTIO_POD=$(kubectl get pods -l app=fortio -o 'jsonpath={.items[0].metadata.name}')

    kubectl exec "$FORTIO_POD" -c fortio -- fortio load -c 2 -qps 0 -t 200s -loglevel Warning http://nginx-service:8000
    kubectl exec "$FORTIO_POD" -c fortio -- fortio load -c 2 -qps 0 -n 20 -loglevel Warning http://nginx-service:8000


Consistent Hash:
    curl --header "x-user: xpto" http://nginx-service:8000


Fault Injection:
    https://istio.io/latest/docs/tasks/traffic-management/fault-injection/

Circuit Breaker:
    https://istio.io/latest/docs/reference/config/networking/destination-rule/#OutlierDetection
    
    kubectl exec "$FORTIO_POD" -c fortio -- fortio load -c 2 -qps 0 -n 20 -loglevel Warning http://servicex-service


kubectl exec -it nginx-c9545d5fc-d5ctn -c nginx -- bash

kubectl edit service istio-ingressgateway -n istio-system