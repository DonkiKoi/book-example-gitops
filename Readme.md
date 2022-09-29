## Setup ArgoCD Applications 


Install the ArgoCD Operator. Create `gitops` namespace, go to _Installed Operators/ArgoCD_ and create a new ArgoCD cluster instance (_Argo CD --> Create ArgoCD_). Use the file `argocd-cluster.yaml` as template. Important is here corroct configuration of RBAC

```yml
  rbac:
    defaultPolicy: role:admin
    policy: |
      g, system:cluster-admins, role:admin
      g, cluster-admins, role:admin
    scopes: '[groups]'
```

Create the target namespaces and necessary role bindings

```bash
$ oc apply -k argocd/infra/
namespace/person-dev created
namespace/person-stage created
role.rbac.authorization.k8s.io/argocd-access-role created
role.rbac.authorization.k8s.io/argocd-access-role created
rolebinding.rbac.authorization.k8s.io/person-dev-admin-role-binding created
rolebinding.rbac.authorization.k8s.io/person-dev-edit-role-binding created
rolebinding.rbac.authorization.k8s.io/person-stage-admin-role-binding created
rolebinding.rbac.authorization.k8s.io/person-stage-edit-role-binding created
```

Login into ArgoCD UI, edit the _in-cluster_ in _Settings/Clusters_ and add `person-dev,person-stage` to `NAMESPACES`.

Deploy the applications

```bash
$ oc apply -f argocd/person-apps.yaml
application.argoproj.io/postgresql-dev created
application.argoproj.io/postgresql-stage created
application.argoproj.io/person-dev created
application.argoproj.io/person-stage created
```

The web hook (for Git repo) is available under `<ARGOCD URL>/api/webhook`

Define a webhook secret in  and use it when configuring webhook in git repositoory (https://argo-cd.readthedocs.io/en/stable/operator-manual/webhook/)

```yml
stringData:
  # github webhook secret
  webhook.github.secret: xxxxxxxxxxxxxxxxx
```
