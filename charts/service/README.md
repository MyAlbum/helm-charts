# BaseChart
Base Helm Chart for services

## Values

### Root
```YAML
image: nginx:1.16
pullPolicy: IfNotPresent
```

### Deployment
```YAML
deployment:
  command: null
  port: 80
  replicaCount: 1
  healthcheck:
    enabled: true
    path: /
    port: 80
```

### Resources
```YAML
resources:
  requests:
    cpu: 100m
    memory: 128Mi
```

### Service
```YAML
service:
  create: true
  port: 80
  nodePort: 30000
```

### Service account
```YAML
serviceAccount:
  create: true
  isKubeAdmin: false
  annotations:
    eks.amazonaws.com/role-arn: "<role arn>"
  role:
    create: true
    rules:
    - apiGroups: ["batch"]
      resources: ["jobs"]
      verbs: ["get", "watch", "list", "create", "delete"]
```

### Ingress
```YAML
ingress:
  create: true
  hosts:
    - host: example.com
      paths:
        - /nginx
```

### Cronjobs
```YAML
cronjobs:
  enabled: true
  jobs:
    - name: test
      image: {}
      schedule: "*/1 * * * *"
      securityContext: {}
      restartPolicy: OnFailure
      command: ["node", "cron/test.js"]
```

### HPA on CPU
```YAML
hpa:
  create: true
  spec:
    minReplicas: 1
    maxReplicas: 10
    targetCPUUtilizationPercentage: 80
```

### HPA on SQS size
```YAML
sqsMetric:
  create: true
  metricName: sqs-example-length
  queueName: example
  period: 30

hpa:
  create: true
  apiVersion: autoscaling/v2beta1
  spec:
    minReplicas: 1
    maxReplicas: 10
    metrics:
    - type: External
      external:
        metricName: sqs-example-length
        targetAverageValue: 10
```

### Env vars
```YAML
env:
  TEST: 1
```

### Secrets
```YAML
secrets:
  TEST: 1
```

### SecretRefs
```YAML
secretRefs:
  TEST:
    secret: myalbum-generic
    key: TEST
```
