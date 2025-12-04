Update kubeconfig to EKS Cluster

aws eks update-kubeconfig \
  --name ferocious-classical-sheepdog \
  --region us-west-1
  

Export Github Token
export GITHUB_TOKEN=


Flux bootstrap customization

Create placeholder files
mkdir clusters/my-cluster/flux-system
touch clusters/my-cluster/flux-system/gotk-components.yaml \
    clusters/my-cluster/flux-system/gotk-sync.yaml \
    clusters/my-cluster/flux-system/kustomization.yaml
    
Update kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources: # manifests generated during bootstrap
  - gotk-components.yaml
  - gotk-sync.yaml
  
  
Bootstrap fluxcdrepo
flux bootstrap github \
  --token-auth \
  --owner=sumitsks1312 \
  --repository=fluxcdrepo \
  --branch=main \
  --path=clusters/my-cluster \
  --personal
  

Validate flux cd deployment
kubectl get pods -n flux-system

Other commands
kubectl get gitrepo -n flux-system
NAME          URL                                              AGE     READY   STATUS
flux-system   https://github.com/sumitsks1312/fluxcdrepo.git   3m27s   True    stored artifact for revision 'main@sha1:11d119ffb075ec4d632c646c4899eec8b31bbdca'

kubectl get secrets -n flux-system
NAME          TYPE     DATA   AGE
flux-system   Opaque   2      4m24s


kubectl get clusterrolebindings | grep flux
cluster-reconciler-flux-system                                  ClusterRole/cluster-admin                                                   7m55s
crd-controller-flux-system                                      ClusterRole/crd-controller-flux-system                                      7m54s
