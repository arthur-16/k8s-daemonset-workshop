# k8s-daemonset-workshop

# Preliminary steps

Please start up docker and run:
```
minikube start
```
Run kubectl get pods to make sure the minikube cluster is working properly.

# Step 1 - Configure Agent permissions

If your Kubernetes has role-based access control (RBAC) enabled, configure RBAC permissions for your Datadog Agent service account. From Kubernetes 1.6 onwards, RBAC is enabled by default. Create the appropriate ClusterRole, ServiceAccount, and ClusterRoleBinding with the following command:

```
kubectl apply -f "https://raw.githubusercontent.com/DataDog/datadog-agent/master/Dockerfiles/manifests/rbac/clusterrole.yaml"

kubectl apply -f "https://raw.githubusercontent.com/DataDog/datadog-agent/master/Dockerfiles/manifests/rbac/serviceaccount.yaml"

kubectl apply -f "https://raw.githubusercontent.com/DataDog/datadog-agent/master/Dockerfiles/manifests/rbac/clusterrolebinding.yaml"
```

# Step 2 - Create the Datadog Agent manifest

Since our use case for this workshop is Daemonset with only Metrics & Logs, create the datadog-agent-logs.yaml manifest out of the following template:

https://docs.datadoghq.com/resources/yaml/datadog-agent-logs.yaml

Add it into the same directory you're working in for quick accessibility.

# Step 3 - Create Secret

In the **secret-api-key.yaml manifest**, replace **PUT_YOUR_BASE64_ENCODED_API_KEY_HERE** with your Datadog API key encoded in base64. To get the base64 version of your API key, you can run:

```
echo -n '<Your API key>' | base64
```

# Step 4 - Add Site

Set your Datadog site to **datadoghq.com** using the **DD_SITE** environment variable in the **datadog-agent.yaml** manifest.

If the DD_SITE environment variable is not explicitly set, it defaults to the US site datadoghq.com. If you are using one of the other sites (EU, US3, or US1-FED) this will result in an invalid API key message. Use the [documentation site selector](https://docs.datadoghq.com/getting_started/site/) to see documentation appropriate for the site you’re using.

# Step 5 - Deploy Daemonset

Deploy the DaemonSet with the command:

```
kubectl apply -f datadog-agent.yaml
```

# Step 6 - Verify Daemonset 

Verification: To verify the Datadog Agent is running in your environment as a DaemonSet, execute:

```
kubectl get daemonset
```

If the Agent is deployed, output similar to the text below appears, where DESIRED and CURRENT are equal to the number of nodes running in your cluster.

```
NAME            DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
datadog-agent   2         2         2         2            2           <none>          10s
```
