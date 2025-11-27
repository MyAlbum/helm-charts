# helm-charts

MyAlbum Public Helm Chart Repo

# Service test values.yaml

```yaml
image: nginx:latest

service:
  create: true
  port: 80

ingress:
  create: true
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /$1
  hosts:
    - host: andre.myalbumdev.com
      paths:
        - /nginx

hpa:
  create: true
  spec:
    minReplicas: 1
    maxReplicas: 10
    targetCPUUtilizationPercentage: 80
```

# App-template example values.yaml

```yaml
controllers:
  frontend:
    type: deployment
    labels:
      app: account-frontend
    replicas: 1
    containers:
      frontend:
        image:
          repository: 732584575412.dkr.ecr.eu-west-1.amazonaws.com/myalbum-dev/account-service
          tag: main-77acc2a67be7064acfc4dbb659a656acb798078f
          pullPolicy: IfNotPresent
        command: ["yarn", "start:frontend"]
service:
  frontend:
    controller: frontend
    type: ClusterIP
    ports:
      http:
        port: 3000
ingress:
  frontend:
    className: nginx
    hosts:
      - host: account.myalbumdev.com
        paths:
          - path: /
            pathType: Prefix
            service:
              identifier: frontend
rawResources:
  frontend-hpa:
    apiVersion: autoscaling/v2
    kind: HorizontalPodAutoscaler
    metadata:
      name: frontend-hpa
    spec:
      spec:
        scaleTargetRef:
          apiVersion: apps/v1
          kind: Deployment
          name: frontend
        minReplicas: 1
        maxReplicas: 10
        metrics:
          - type: Resource
            resource:
              name: cpu
              target:
                type: Utilization
                averageUtilization: 80
```
