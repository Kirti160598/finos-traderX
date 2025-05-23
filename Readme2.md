 ## 🛡️ Trivy Vulnerability Scanning in Jenkins Pipeline
  Jenkins Pipeline Stage

```groovy
stage('Scan Docker Image') {
    steps {
        // Display Trivy version for verification
        bat 'trivy --version'

        // Clean previous Java vulnerability database cache
        bat 'trivy clean --java-db'

        // Scan the Docker image and save results in JSON format
        bat 'trivy image --timeout 10m --format json -o trivy-report.json %DOCKER_IMAGE%'
    }
}
```
✅ The scan output is saved to trivy-report.json for analysis or integration with reporting tools.


## 🚀 Kubernetes Deployment Strategy: Rolling Update

The Kubernetes deployment uses a Rolling Update strategy to ensure zero downtime during application updates. This strategy gradually replaces old pods with new ones, allowing your service to stay available.

Configuration snippet from deployment.yaml
```
spec:
  replicas: 2
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
      maxSurge: 1
```

📘 Explanation

replicas: 2 — Ensures high availability with two running pods.

strategy.type: RollingUpdate — Updates pods incrementally rather than all at once.

maxUnavailable: 1 — Allows at most one pod can be unavailable during an update.

maxSurge: 1 — Allows one extra pod to be created above the desired number of replicas during the update.

🔁 Undo a Deployment Rollout

```kubectl rollout undo deployment/database-deployment -n database-flux```


📊 Check Rollout Status

```kubectl rollout status deployment/database-deployment -n database-flux```

🕓 View Rollout History

```kubectl rollout history deployment/database-deployment -n database-flux```

Output of rollout history
```
deployment.apps/database-deployment 
REVISION  CHANGE-CAUSE
1         <none>
2         <none>
```
⏪ Rollback to a Specific Revision

```kubectl rollout undo deployment/database-deployment -n database-flux --to-revision=1```


Will work with readiness and liveness probe to rollback 

## Monitoring with Grafana and Promotheus

### 📥 Downloaded istioctl

```https://github.com/istio/istio/releases/tag/1.26.0            ```

### Start minikube 
```
vCPUs: 2 minimum (preferably 4)
Memory: 4 GB minimum (preferably 8 GB)
minikube start --cpus=4 --memory=8192 --driver=docker
```
### Installation of Istio in your Kubernetes cluster and Istio to install the demo configuration

```istioctl install --set profile=demo -y```

Explanation:

```
istioctl install
This is the command to install Istio in your Kubernetes cluster (Minikube in your case)
 ```
```
--set profile=demo
This flag tells Istio to install the demo configuration, which includes:
```
```
Component	            Description
istiod	            Istio control plane
istio-ingress	Ingress gateway for exposing services
prometheus	            Metrics collection
grafana	            Dashboard for metrics visualization
kiali	            UI for Istio mesh topology
jaeger	            Distributed tracing system
```

#### To Check the Istio pods
```
kubectl get pods -n istio-system
```

#### Enabling automatic sidecar injection for the default namespace in your Kubernetes cluster.
```
kubectl label namespace default istio-injection=enabled
```

means that Istio will automatically inject an Envoy sidecar proxy into every new Pod that gets created in that namespace.

#### Enable Istio’s Telemetry Add-ons
Istio comes with Grafana, Prometheus, Jaeger, and Kiali.
Apply the telemetry add-ons:
```
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.21/samples/addons/grafana.yaml
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.21/samples/addons/prometheus.yaml
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.21/samples/addons/jaeger.yaml
```

####  Access Grafana Dashboard
You can access Grafana using port-forwarding
``` kubectl port-forward svc/grafana 3000:3000 -n istio-system ```




