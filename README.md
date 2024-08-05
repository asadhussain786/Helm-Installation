## What is Helm?

Helm is often referred to as the package manager for Kubernetes. It enables you to define, install, and manage even the most complex Kubernetes applications. Helm uses a packaging format called charts, which include all the resources needed to run an application, service, or a complete cloud-native stack inside Kubernetes.

## Step 1: How to Install Helm in Ubuntu?

```bash
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
sudo apt-get install apt-transport-https --yes
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm
```

### Step 2: Create Helm Chart Structure

Run the following command to scaffold a new Helm chart:
```bash
helm create node-app-chart
```
This will create a folder named `node-app-chart` with the initial chart structure.

### Step 3: Chart Metadata (Chart.yaml)
Open Chart.yaml and modify it for your application:
```bash
apiVersion: v2
name: node-app-chart
description: A Helm chart for a Node.js application
version: 0.1.0
```

### Step 4: Default Values (values.yaml)
Edit values.yaml to include:
```bash
replicaCount: 1

image:
  repository: ayihandocker/2-tier-flask-app:flask-app
  tag: flask-app
  pullPolicy: IfNotPresent

service:
  type: NodePort
  port: 30007
  targetPort: 8000
```

### Step 5:  Deployment Manifest (templates/deployment.yaml)
Modify deployment.yaml under templates/ to look like this:
```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-deployment
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app: {{ .Release.Name }}
  template:
    metadata:
      labels:
        app: {{ .Release.Name }}
    spec:
      containers:
      - name: {{ .Chart.Name }}
        image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
        imagePullPolicy: {{ .Values.image.pullPolicy }}
        ports:
        - containerPort: 8000
```
### Step 6: Service Manifest (templates/service.yaml)
Open service.yaml under templates/ and modify it:
``` bash
apiVersion: v1
kind: Service
metadata:
  name: {{ .Release.Name }}-service
spec:
  type: {{ .Values.service.type }}
  ports:
  - port: {{ .Values.service.port }}
    targetPort: {{ .Values.service.targetPort }}
    nodePort: {{ .Values.service.port }}
  selector:
    app: {{ .Release.Name }}
```
### Step 7: Deploying the Helm Chart
Use these commands to package and deploy the chart:

# Package the chart (Optional)
``` bash
helm package node-app-chart
```
# Deploy the chart
``` bash
helm install my-node-app ./node-app-chart
```

### Step 8: List the Deployment

# list the charts (Optional)
``` bash
helm list
```
# Check the status
``` bash
helm status <chart name>
```
### Important Helm Commands

- `helm create [CHART]`: Scaffold a new Helm chart.
- `helm package [CHART]`: Package the chart into a chart archive.
- `helm install [NAME] [CHART]`: Install a Helm chart.
- `helm upgrade [NAME] [CHART]`: Upgrade an installed Helm chart.
- `helm uninstall [NAME]`: Uninstall an installed Helm chart.
- `helm list`: List all installed Helm charts.
- `helm rollback [NAME] [REVISION]`: Roll back a release to a specific revision.
