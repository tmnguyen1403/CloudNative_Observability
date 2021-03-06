## Cloud Native Architecture Nanodegree (CNAND): Observability

This is the public repository for the Observability course of Udacity's Cloud Native Architecture Nanodegree (CNAND) program (ND064).

The  **Exercise_Starter_Files** directory has all of the files you'll need for the exercises found throughout the course.

The **Project_Starter_Files** directory has the files you'll need for the project at the end of the course.

Useful commands:
kubectl create namespace monitoring
# Added prometheus repo 
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
# Install prometheus with grafana
helm install prometheus prometheus-community/kube-prometheus-stack --namespace monitoring --kubeconfig /etc/rancher/k3s/k3s.yaml
kubectl get pods --namespace=monitoring
#create load balancer for prometheus-grafana
kubectl patch svc "prometheus-grafana" --namespace "monitoring" -p '{"spec":{"type" : "LoadBalancer"}}'
#forwarded port 80 to 3000(default grafana port) on virtual machine
kubectl --namespace monitoring port-forward svc/prometheus-grafana --address 0.0.0.0 3000:80
#Login to grafana
localhost:3000
username: admin
password: prom-operator

#INSTALL Jaeger
kubectl create namespace observability

# Please use the last stable version
export jaeger_version=v1.28.0
# jaegertracing.io_jaegers_crd.yaml
kubectl create -f https://raw.githubusercontent.com/jaegertracing/jaeger-operator/${jaeger_version}/deploy/crds/jaegertracing.io_jaegers_crd.yaml
# service_account.yaml
kubectl create -n observability -f https://raw.githubusercontent.com/jaegertracing/jaeger-operator/${jaeger_version}/deploy/service_account.yaml
# role.yaml
kubectl create -n observability -f https://raw.githubusercontent.com/jaegertracing/jaeger-operator/${jaeger_version}/deploy/role.yaml
# role_binding.yaml
kubectl create -n observability -f https://raw.githubusercontent.com/jaegertracing/jaeger-operator/${jaeger_version}/deploy/role_binding.yaml
# operator.yaml
kubectl create -n observability -f https://raw.githubusercontent.com/jaegertracing/jaeger-operator/${jaeger_version}/deploy/operator.yaml

# cluster_role.yaml
kubectl create -f https://raw.githubusercontent.com/jaegertracing/jaeger-operator/${jaeger_version}/deploy/cluster_role.yaml
# cluster_role_binding.yaml
kubectl create -f https://raw.githubusercontent.com/jaegertracing/jaeger-operator/${jaeger_version}/deploy/cluster_role_binding.yaml

# VERIFY
kubectl get deployments jaeger-operator -n observability
kubectl get pods,svc -n observability
# OPTIONAL - Deploy a sample application
# Note that the command below is an updated one.
kubectl apply -f https://raw.githubusercontent.com/jaegertracing/jaeger-operator/${jaeger_version}/examples/business-application-injected-sidecar.yaml
