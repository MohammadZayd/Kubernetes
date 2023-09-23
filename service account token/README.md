# Project Documentation

This README provides documentation for the setup and configuration of the project. It includes step-by-step instructions and explanations for various components.

---

## Setting up Dashboard Pod and Service

### Manifest: `my-dashboard-pod.yaml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-dashboard-pod
  labels:
    app: dashboard
spec:
  containers:
  - name: dashboard-container
    image: <your-dashboard-image>
    ports:
    - containerPort: 8080
```


### Manifest: `my-dashboard-pod.yaml`

This manifest creates a Kubernetes Service for the Dashboard application, which will expose it on port 8080. The Service will also be named `my-dashboard-service.yaml`.

```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: dashboard
  name: my-dashboard-svc
spec:
  ports:
  - port: 8080
    protocol: TCP
    targetPort: 8080
  selector:
    app: dashboard
  type: NodePort
```

## Setting up Service Account



```shell
kubectl create serviceaccount dashboard-sa
```

Create a Service Account named dashboard-sa using the dashboard-sa.yaml manifest.

## Creating Role and RoleBinding

### Manifest: `dashboard-role.yaml`

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups:
  - ''
  resources:
  - pods
  verbs:
  - get
  - watch
  - list

```

### Manifest: `service account token/dashboard-RBAC.yaml`

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: default
subjects:
- kind: ServiceAccount
  name: dashboard-sa
  namespace: default
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```
Create a Role named pod-reader in the default namespace using the dashboard-role.yaml manifest.

Create a RoleBinding named read-pods in the default namespace that binds the pod-reader Role to the dashboard-sa Service Account using the dashboard-RBAC.yaml manifest.

## Generating and Using Token
To generate a token for the dashboard-sa Service Account, you can use the kubectl command:

```shell
kubectl create token dashboard-sa
```
Use the generated token in your Dashboard Pod to authenticate and access information about all the pods in the cluster.

