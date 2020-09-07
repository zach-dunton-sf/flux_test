# flux_test

## Installing flux onto the cluster
Create the `flux` namespace
```
kubectl create namespace flux
```

Install the `flux` service
```
export GHUSER="YOURUSER"
fluxctl install \
--git-user=${GHUSER} \
--git-email=${GHUSER}@users.noreply.github.com \
--git-url=git@github.com:${GHUSER}/flux_test \
--git-path=namespaces,workloads \
--namespace=flux | kubectl apply -f -
```

Get the flux public key
```
fluxctl identity --k8s-fwd-ns flux
```

Then add it as a deploy key to github