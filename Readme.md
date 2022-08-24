
## Setup ArgoCD in EKS 

1. Run `Terraform` eks project and create aws eks cluster 
2. Update `kubeconfig` 
```
aws eks --region ap-southeast-1 update-kubeconfig --name <eks cluster name>
```
3. Create `namespace` argocd

```
kubectl create ns argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

```
4. Verify the deployments 
```
kubectl get all -n argocd
```
5. Get the Initial Admin Password to access ArgoCD Server and create a LoadBalancer in AWS 
```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'


```
6. Verify the Service (Loadbalancer)
```
kubectl get svc -n argocd
```
7. Access the ArgoCD Server Web UI using `admin` and password from Step 5 after port forwarding to localhost 
```
kubectl port-forward svc/argocd-server -n argocd 8080:443
# Open a New Terminal
argocd login localhost:8080
```
 ### Delete the installation 

 kubectl delete -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

## Create ArgoCD `Application` 

1. kubectl config get-contexts -o name
2. argocd cluster add arn:aws:eks:ap-southeast-1:xxxx:cluster/scpay-drs
3. kubectl apply -f ../argocd-foundations/application.yml
4. kubectl get application -n argocd
5. argocd app list

### CLI 

argocd app create app-2 --repo https://github.com/mabusaa/argocd-example-apps.git --revision master --path guestbook --dest-server https://kubernetes.default.svc --dest-namespace app-2 --sync-option CreateNamespace=true

1. argocd app list
2. argocd app sync app-2

