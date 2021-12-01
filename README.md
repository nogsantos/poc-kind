# Kind

> [kind](https://sigs.k8s.io/kind) is a tool for running local Kubernetes clusters using Docker container “nodes”. kind was primarily designed for testing Kubernetes itself, but may be used for local development or CI.

## Setup

- Install [kind](https://kind.sigs.k8s.io/docs/user/quick-start/#installation)

On linux

```bash
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.11.1/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/
```

After the process, to check:

```bash
kind
```

Should output:

```bash
kind creates and manages local Kubernetes clusters using Docker container 'nodes'

Usage:
  kind [command]

Available Commands:
  build       Build one of [node-image]
  completion  Output shell completion code for the specified shell (bash, zsh or fish)
  create      Creates one of [cluster]
  delete      Deletes one of [cluster]
  export      Exports one of [kubeconfig, logs]
  get         Gets one of [clusters, nodes, kubeconfig]
  help        Help about any command
  load        Loads images into nodes
  version     Prints the kind CLI version

Flags:
  -h, --help              help for kind
      --loglevel string   DEPRECATED: see -v instead
  -q, --quiet             silence all stderr output
  -v, --verbosity int32   info log verbosity
      --version           version for kind

Use "kind [command] --help" for more information about a command.
```

- Create the cluster

```bash
kind create cluster --name nogsantos-ingress --config kind-ingress.yml
```

After the process, to check:

```bash
kubectl get nodes
```

Should output:

```bash
NAME                              STATUS   ROLES                  AGE   VERSION
nogsantos-ingress-control-plane   Ready    control-plane,master   20m   v1.21.1
nogsantos-ingress-worker          Ready    <none>                 20m   v1.21.1
```

- Using nginx as Ingress controller

Install the [Nginx](https://kind.sigs.k8s.io/docs/user/ingress/#ingress-nginx)

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
```

- Create the pods with kubctl

```bash
kubectl create -f kind-test-ingress.yml
```

After the process, to check:

```bash
kubectl get pods
```

Should output:

```bash
NAME          READY   STATUS    RESTARTS   AGE
api           1/1     Running   0          10m
app-service   1/1     Running   0          10m
```

To verify if the ingress works

```bash
curl localhost/nogsantos-api
# should output "NOGSANTOS API"
curl localhost/service
# should output "APP AS A SERVICE"
```

In the browser, `http://localhost/nogsantos-api` or ``http://localhost/service``

> TIP: You can use (Lens)[https://docs.k8slens.dev/main/getting-started/] as Kubernetes IDE

### Removing the klusters

- Get cluster name

```bash
kind get clusters
```

Should output:

```bash
nogsantos-ingress
```

- Delete the cluster

```bash
kind delete clusters nogsantos-ingress
```

Should output:

```bash
Deleted clusters: ["nogsantos-ingress"]
```
