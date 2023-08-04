# Opentelemetry K8S Auto instrumentation



- Install Certmanager
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.8.0/cert-manager.yaml

- Install Opentelemetry Operator
kubectl apply -f https://github.com/open-telemetry/opentelemetry-operator/releases/latest/download/opentelemetry-operator.yaml

(It creates opentelemetry-operator-system namespace)

* Check
kubectl get all -n opentelemetry-operator-system

* Custom Values
kubectl apply -f opentelemetry-operator.yaml

- Create namespace opentelemetry
kubectl create namespace opentelemetry

- Deploy Opentelemetry collector
kubectl apply -f collector.yaml

- Create namespace app for deploy our app's
kubectl create namespace app
 
- Create a sidecar for instrumented pods (configured to be deployed on app namespace)
kubectl apply -f sidecar.yml

- Create the instrumentation (configured to be deployed on app namespace)
kubectl apply -f instrumentation.yml

- Deploy your auto instrumented app

kubectl apply -f app-deploy.yaml

* Instrument your app in it's deployment as follows:

spec:
template:
  metadata:
    annotations:
      instrumentation.opentelemetry.io/inject-python: 'python-instrumentation'
      sidecar.opentelemetry.io/inject: 'sidecar'

* For minikube, if image download fails, use local images
minikube docker-env
eval $(minikube -p minikube docker-env)
minikube image ls --format table
minikube image load registry.gitlab.com/telecom-argentina/coo/flow/op/taskmanager-flow:opentelemetry-auto_947572262


