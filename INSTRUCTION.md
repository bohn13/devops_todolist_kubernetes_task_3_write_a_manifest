# Instruction for ToDo Application Deployment

This document provides instructions on how to deploy the ToDo application in a Kubernetes cluster and verify its functionality.

## 1. Prerequisites

* A running Kubernetes cluster (e.g., Docker Desktop, Minikube).
* `kubectl` command-line tool configured to communicate with your cluster.

## 2. Deployment Steps

Follow these steps in order to apply the manifests located in the `.infrastructure` folder:

1. **Create the Namespace:**
```bash
kubectl apply -f .infrastructure/namespace.yml
```


2. **Deploy the Busybox (Testing Tool):**
```bash
kubectl apply -f .infrastructure/busybox.yml
```


3. **Deploy the ToDo Application:**
```bash
kubectl apply -f .infrastructure/todoapp-pod.yml
```


4. **Verify Pods Status:**
Check if the pods are running and the ToDo application has passed its readiness probes:
```bash
kubectl get pods -n todoapp
```



---

## 3. Testing the Application

### Method A: Using `port-forward` (Local Access)

The `port-forward` command allows you to access the application running inside the cluster from your local machine.

1. **Start the port-forwarding:**
```bash
kubectl port-forward pod/todoapp-app 8080:8080 -n todoapp
```


2. **Access the application:**
Open your browser or run `curl` on your local machine:
* Health check: [http://localhost:8080/api/health/](https://www.google.com/search?q=http://localhost:8080/api/health/)
* Readiness check: [http://localhost:8080/api/ready/](https://www.google.com/search?q=http://localhost:8080/api/ready/)


### Method B: Using `busyboxplus:curl` (Internal Cluster Access)

This method tests the communication between containers within the same namespace.

1. **Get the Pod IP address:**
```bash
kubectl get pod todoapp-app -n todoapp -o wide
```

2. **Execute curl from the Busybox container:**
Use the IP address obtained in the previous step (e.g., `10.1.0.15`):
```bash
kubectl exec -it busybox -n todoapp -- curl http://<POD_IP>:8080/api/health/
```

*Note: Replace `<POD_IP>` with the actual IP address of the `todoapp-app` pod.*

---

## 4. Troubleshooting

If the `todoapp-app` is not ready, check the logs:

```bash
kubectl logs todoapp-app -n todoapp
```

To see detailed events (e.g., probe failures):

```bash
kubectl describe pod todoapp-app -n todoapp
```
