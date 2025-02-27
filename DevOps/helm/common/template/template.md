## Templates

To parameterize Kubernetes manifest files define a common template that contains placeholders
for where microservice-specific values will be placed in the template, when a manifest is rendered for a specific
microservice.<br>
`kubernetes/helm/common/templates/_deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "common.fullname" . }}
spec:
  replicas: {{ .Values.replicaCount }}
  template:
    spec:
      containers:
        - name: {{ .Chart.Name }}
```
It is different from the standard Kubernetes manifest file because it uses `{{ }}` to define placeholders for values.
- `{{ include "common.fullname" . }}` is used to invoke other templates.

The other two constructs are used to insert values using one of the built-in objects in Helm.<br>
The most frequently used parts of the built-in objects are:
- `.Values` Used to refer to values in the chart’s values.yaml
- `Release` Used to provide metadata regarding the current release that is installed
    - `Release.Name` The name of the release
    - `Release.Namespace` The namespace of the release
    - `Release.Service` The service that is used to install the release(helm)
- `Chart` Used to access information from the Chart.yaml file
    - `Chart.Name` The name of the chart
    - `Chart.Version` The version of the chart
- `Files` Containing functions for accessing chart-specific files
    - `Files.Glob` Returns files in a chart based on a glob pattern.<br>
      For example, the pattern "config-repo/*" will return all files found in the folder config-repo
    - `AsConfig` Returns the content of files as a YAML map appropriate for declaring values in a ConfigMap
    - `Files.GetSecret` Used to get the contents of a secret
- `Capabilities` an be used to find information regarding the capabilities of the Kubernetes cluster that the installation is performed on.<br>
  For example, a template can use information in this object to adopt a manifest based on what API versions the actual Kubernetes cluster supports.

The single dot `.` is used to refer to the current scope.<br>
The dollar sign `$` is used to refer to the root scope.<br>
Helm templates support the declaration of variables to reference other objects
```yaml
$name := .Release.Name
```







