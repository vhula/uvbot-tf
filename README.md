# Deployment of UV Bot in FluxCD

Please, follow these steps to deploy the UV Bot once the FluxCD is up and running:

1. Add a namespace file in flux-gitops repository, i.e. flux-gitops/clusters/uvbot/ns.yaml:

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: uvbotns
```

2. Add a GitRepository file in flux-gitops repository, i.e. flux-gitops/clusters/uvbot/uvbot-gr.yaml:

```yaml
apiVersion: source.toolkit.fluxcd.io/v1
kind: GitRepository
metadata:
  name: uvbot
  namespace: uvbotns
spec:
  interval: 1m0s
  ref:
    branch: develop
  url: https://github.com/vhula/uvbot
```

3. Add a HelmRelease file in flux-gitops repository, i.e. flux-gitops/clusters/uvbot/uvbot-hr.yaml:

```yaml
apiVersion: helm.toolkit.fluxcd.io/v2beta1
kind: HelmRelease
metadata:
  name: uvbot
  namespace: uvbotns
spec:
  chart:
    spec:
      chart: ./helm
      reconcileStrategy: ChartVersion
      sourceRef:
        kind: GitRepository
        name: uvbot
  interval: 1m0s
```

**Each commit in uvbot repository will generate a new version of Docker image and increment a version in Chart.yaml. Flux will detect each version change in Chart.yaml and redeploy the UV Bot.**
