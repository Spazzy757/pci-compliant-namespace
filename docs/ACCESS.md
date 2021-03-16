# Access

Managing Access with Kubernetes is quite straight forward. Kubernetes uses a policy known as [Role Based Access Control](https://en.wikipedia.org/wiki/Role-based_access_control), this essentially means that you create a role that has permissions to do actions and then you are able to attach users or groups to this role, allowing them to perform said actions. RBAC in kubernetes [went beta in 1.6](https://kubernetes.io/blog/2017/04/rbac-support-in-kubernetes/#:~:text=RBAC%2C%20Role%2Dbased%20access%20control,be%20updated%20without%20cluster%20restarts.) and subsequentially become GA.

## How does it work?

You have 4 resources that you should be concerned with:

- [ClusterRole](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#role-and-clusterrole)
- [ClusterRoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding)
- [Role](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#role-and-clusterrole)
- [RoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding)

The simple difference between a Role and a ClusterRole is the scope. A Role is scoped to a namespace while a ClusterRole is scoped to a cluster, this is similar for the ClusterRoleBindings and RoleBindings, for instance creating a role that has read access to a pod

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: pod-reader
rules:
  - apiGroups: [""] # "" indicates the core API group
    resources: ["pods"]
    verbs: ["get", "watch", "list"]
```

This Role will only be available in the default namespace. We can attach this role to a user by specifying it in a RoleBinding

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: default
subjects:
  # You can specify more than one "subject"
  - kind: User
    name: read-user # "name" is case sensitive
    apiGroup: rbac.authorization.k8s.io
roleRef:
  # "roleRef" specifies the binding to a Role / ClusterRole
  kind: Role #this must be Role or ClusterRole
  name: pod-reader # this must match the name of the Role or ClusterRole you wish to bind to
  apiGroup: rbac.authorization.k8s.io
```

The user _read-user_ will now have the ability to read pods in the default namespace.

For a ClusterRole and RoleBinding:

```yaml
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  # "namespace" omitted since ClusterRoles are not namespaced
  name: secret-reader
rules:
  - apiGroups: [""]
    #
    # at the HTTP level, the name of the resource for accessing Secret
    # objects is "secrets"
    resources: ["secrets"]
    verbs: ["get", "watch", "list"]
---
apiVersion: rbac.authorization.k8s.io/v1
# This cluster role binding allows anyone in the "manager" group to read secrets in any namespace.
kind: ClusterRoleBinding
metadata:
  name: read-secrets-global
subjects:
  - kind: Group
    name: manager # Name is case sensitive
    apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: secret-reader
  apiGroup: rbac.authorization.k8s.io
```

The above will grant the user _manager_ global access to secrets.

One little known fact is that you can mix ClusterRoles and RoleBindings, creating a Global Role that is accessabile in all namepsaces but reducing the access of the binding to a single namespace

```yaml
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  # "namespace" omitted since ClusterRoles are not namespaced
  name: secret-reader
rules:
  - apiGroups: [""]
    #
    # at the HTTP level, the name of the resource for accessing Secret
    # objects is "secrets"
    resources: ["secrets"]
    verbs: ["get", "watch", "list"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-secrets
  #
  # The namespace of the RoleBinding determines where the permissions are granted.
  # This only grants permissions within the "development" namespace.
  namespace: development
subjects:
  - kind: User
    name: dev-user # Name is case sensitive
    apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: secret-reader
  apiGroup: rbac.authorization.k8s.io
```

The above will take the global permission of _secret-reader_ and give the user _dev-user_ access to it but only in the `development` namespace. This allows us to create a single Role but reduce scope to a namespace

## OpenID Connect Integration

Keeping in mind the previous understandings of Roles and Bindings, you also get another feature within Kubernetes and that is [OpenID Connect integrations](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#openid-connect-tokens). If using a Cloud Provider this means you can integrate your cluster into your Groups that you are using to manage your other resources. Meaning that a Azure Active Directory User or a Google Gsuite User could potentially be granted access (or denied access) to resources on the cluster

## How is this useful?

One of the key concerns of PCI compliance is access to the system. If you are working in the mindset of a single cluster this means that you can have clearly defined groups that can access specific namespaces while opening up access to other namespaces.
