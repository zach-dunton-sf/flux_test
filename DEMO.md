# Kustomize Kubernetes Demo
Using `kustomize` to deploy c3po-sleep (https://github.com/cnry/c3po-sleep)

## Prepare Environment
```
export GHUSER="zach-dunton-sf"
export FLUX_ROOT="/home/zach/Documents/canary/flux_test"
```

## Kustomize overview
`deploy/base/` Contains the base configuration

`deploy/local/` Contains the overrides for KIND

`deploy/minikube/` Contains the overrides for `minikube`

## Deploy to KIND cluster
Show `kustomize` command for KIND `sleep-proxy/k8s/kind/install-kind.sh`

Deploy to KIND cluster
```
make bootstrap-local-cluster
```

## Deploy to Minikube cluster
Setup `minikube` cluster
```
minikube start --driver=kvm2 --insecure-registry="host.minikube.internal:5000"
```

Install `flux`
```
fluxctl install --git-user=${GHUSER} --git-email=${GHUSER}@users.noreply.github.com --git-url=git@github.com:${GHUSER}/flux_test --git-path=namespaces,workloads --namespace=flux | kubectl apply -f -
```

Get the deploy token, and add to github
```
fluxctl identity --k8s-fwd-ns flux
```

Create c3sleep namespace `${FLUX_ROOT}/namspaces/c3sleep.yaml`
```
apiVersion: v1	
kind: Namespace	
metadata:	
  labels:	
    name: c3	
  name: c3 
```

Push config to `flux` and sync
```
cd ${FLUX_ROOT}
git commit . -m "Deploy c3po sleep"
git push origin
fluxctl sync --k8s-fwd-ns flux
```

Use `kustomize` to build the configuration for `minikube`
```
kustomize build deploy/local-minikube/ > ${FLUX_ROOT}/workloads/c3po-sleep.yaml
```

Push config to `flux` and sync
```
cd ${FLUX_ROOT}
git commit . -m "Deploy c3po sleep"
git push origin
fluxctl sync --k8s-fwd-ns flux
```

## Factors to consider for CI/CD
### Flux Paths
* `flux/<env>/namespaces/namespace.yaml`
* `flux/<env>/workloads/<namespace>/<git_repo>.yaml`

### Tooling for creating deployments
* `make namespace c3`

### Pushing to git from CI
Static checks of kubernetes yaml
* kube_score https://github.com/zegl/kube-score
* kubeval https://www.kubeval.com/
* kubesec https://kubesec.io/

### Running automated test suites

### Organizing flux
Directory, branch, or repo per environment?