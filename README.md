# flux_test

## Installig flux onto the cluster
Create the `flux` namespace
```
kubectl create namespace flux
```

```
export GHUSER="YOURUSER"
fluxctl install \
--git-user=${GHUSER} \
--git-email=${GHUSER}@users.noreply.github.com \
--git-url=git@github.com:${GHUSER}/flux_test \
--git-path=namespaces,workloads \
--namespace=flux | kubectl apply -f -
```