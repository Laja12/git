In Kubernetes, you can define environment variables in a deployment YAML file to be used by your application containers. These variables can be specified in the `env` field under the `containers` section of the deployment.

Here's a step-by-step guide on how to define environment variables in a Kubernetes deployment YAML file:

### 1. **Define Environment Variables in the `env` Section**
You can specify environment variables in the `env` section of a container specification. These can be static values or dynamically pulled from Kubernetes resources like ConfigMaps, Secrets, or Downward API.

### Example 1: **Static Environment Variables**
If you want to define static key-value pairs as environment variables, here's how you can do it:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: example-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: example
  template:
    metadata:
      labels:
        app: example
    spec:
      containers:
      - name: example-container
        image: nginx:latest
        env:
        - name: ENV_VAR_1
          value: "value1"
        - name: ENV_VAR_2
          value: "value2"
```

In this example:
- `ENV_VAR_1` and `ENV_VAR_2` are static environment variables with values `"value1"` and `"value2"`, respectively.

### Example 2: **Environment Variables from ConfigMap**
You can pull environment variables from a ConfigMap, which is useful for storing non-sensitive configuration data.

First, create a ConfigMap (`configmap.yaml`):

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: example-configmap
data:
  ENV_VAR_1: "value1"
  ENV_VAR_2: "value2"
```

Then, in your deployment YAML file, you can reference these values:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: example-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: example
  template:
    metadata:
      labels:
        app: example
    spec:
      containers:
      - name: example-container
        image: nginx:latest
        envFrom:
        - configMapRef:
            name: example-configmap
```

In this example:
- `envFrom` is used to reference the entire ConfigMap, meaning all key-value pairs in the ConfigMap are available as environment variables.

### Example 3: **Environment Variables from Secrets**
For sensitive data such as passwords or API keys, you can use Kubernetes Secrets. You can reference Secrets in the `env` or `envFrom` field in a similar manner to ConfigMaps.

First, create a Secret (`secret.yaml`):

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: example-secret
type: Opaque
data:
  password: cGFzc3dvcmQ=  # base64-encoded value of "password"
```

Then, reference the Secret in your deployment YAML:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: example-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: example
  template:
    metadata:
      labels:
        app: example
    spec:
      containers:
      - name: example-container
        image: nginx:latest
        env:
        - name: PASSWORD
          valueFrom:
            secretKeyRef:
              name: example-secret
              key: password
```

In this example:
- The `PASSWORD` environment variable will contain the value stored in the `password` key of the `example-secret` Secret.

### Example 4: **Environment Variables from Downward API**
The Downward API allows you to expose Kubernetes internal information to your containers, such as the Pod name, namespace, labels, etc.

For example, to expose the pod's name as an environment variable:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: example-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: example
  template:
    metadata:
      labels:
        app: example
    spec:
      containers:
      - name: example-container
        image: nginx:latest
        env:
        - name: POD_NAME
          valueFrom:
            fieldRef:
              fieldPath: metadata.name
```

In this example:
- The `POD_NAME` environment variable will be set to the name of the Pod.

### Example 5: **Combining Multiple Sources**
You can combine multiple methods of defining environment variables. For example, you can have static variables, values from a ConfigMap, and values from a Secret all in the same deployment:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: example-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: example
  template:
    metadata:
      labels:
        app: example
    spec:
      containers:
      - name: example-container
        image: nginx:latest
        env:
        - name: STATIC_VAR
          value: "static_value"
        envFrom:
        - configMapRef:
            name: example-configmap
        - secretRef:
            name: example-secret
```

### Summary of Key Elements:
- **`env`**: Used to define individual environment variables as key-value pairs.
- **`envFrom`**: References a ConfigMap or Secret and makes all keys in it available as environment variables.
- **`valueFrom`**: Allows you to dynamically set environment variable values from sources like Secrets, ConfigMaps, or Downward API.

### Conclusion
Defining environment variables in your Kubernetes deployment YAML file is straightforward. You can define static values, reference ConfigMaps or Secrets, and even use the Downward API to expose Kubernetes metadata. This flexibility allows you to securely manage your application's environment and configuration.
