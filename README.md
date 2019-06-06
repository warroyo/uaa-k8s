# Granting cluster access to UAA users

## Developer access

1. make sure `Enable UAA as OIDC provider` is checked in the UAA section on the PKS tile
2. login to UAA using the `uaac` cli

```bash
uaac target https://api.pks.$YOURDOMAIN:8443 --skip-ssl-validation
uaac token client get admin -s '<secret>' #this will be the "Pks Uaa Management Admin Client" in opsman
```

3. create uaa users

```bash
uaac user add dev1 -p '<secret>' --emails 'dev1@localhost.com'
#create as many as you want here
```

4. create a clusterrole and binding for the developers. the file below is a default for a developer setting you can modify the `resources` as needed. also update the `subjects` for all of your users. 

```
kubectl apply -f rbac.yml
```

5. get the kube config from pks for that user

```bash
pks get-kubeconfig <cluster-name> -u dev1 -a https://api.pks.YOURDOMAIN -k -p '<secret>'
kubectl config use-context <cluster-name>
```

6. validate that the user can't do something it shouldn't

```bash
kubectl get nodes                                                                                         
Error from server (Forbidden): nodes is forbidden: User "dev1" cannot list resource "nodes" in API group "" at the cluster scope
```

6. validate the user can do something

```bash
kubectl get pods --all-namespaces
```