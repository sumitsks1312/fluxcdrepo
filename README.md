# FluxCD Bootstrap Guide

This repository contains instructions and manifests for bootstrapping FluxCD on an EKS cluster using GitOps.

## 1. Update kubeconfig to EKS Cluster
```sh
aws eks update-kubeconfig \
  --name ferocious-classical-sheepdog \
  --region us-west-1
```

## 2. Export GitHub Token
```sh
export GITHUB_TOKEN=<your-token>
```

## 3. Flux Bootstrap Customization

### Create placeholder files
```sh
mkdir -p clusters/my-cluster/flux-system

touch clusters/my-cluster/flux-system/gotk-components.yaml \
      clusters/my-cluster/flux-system/gotk-sync.yaml \
      clusters/my-cluster/flux-system/kustomization.yaml
```

### Update kustomization.yaml
Add the following to `clusters/my-cluster/flux-system/kustomization.yaml`:
```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources: # manifests generated during bootstrap
  - gotk-components.yaml
  - gotk-sync.yaml
```

## 4. Bootstrap FluxCD to GitHub
```sh
flux bootstrap github \
  --token-auth \
  --owner=sumitsks1312 \
  --repository=fluxcdrepo \
  --branch=main \
  --path=clusters/my-cluster \
  --personal
```

## 5. Validate FluxCD Deployment
```sh
kubectl get pods -n flux-system
```

## 6. Other Useful Commands
- Check GitRepo status:
  ```sh
  kubectl get gitrepo -n flux-system
  # Example output:
  # NAME          URL                                              AGE     READY   STATUS
  # flux-system   https://github.com/sumitsks1312/fluxcdrepo.git   3m27s   True    stored artifact for revision 'main@sha1:11d119ffb075ec4d632c646c4899eec8b31bbdca'
  ```
- Check secrets:
  ```sh
  kubectl get secrets -n flux-system
  # Example output:
  # NAME          TYPE     DATA   AGE
  # flux-system   Opaque   2      4m24s
  ```
- Check clusterrolebindings:
  ```sh
  kubectl get clusterrolebindings | grep flux
  # Example output:
  # cluster-reconciler-flux-system   ClusterRole/cluster-admin   7m55s
  # crd-controller-flux-system      ClusterRole/crd-controller-flux-system   7m54s
  ```
