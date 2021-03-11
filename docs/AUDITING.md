# Auditing in Kubernetes

## System Interactions
One of the most underestimated features of Kubernetes is [RBAC](https://kubernetes.io/docs/reference/access-authn-authz/rbac/), the methedology of allowing users to be able to perform specifc access. One of the less known features of RBAC is also the ability to track user interactions with the cluster via the [kube apiserver audit logging policies](https://kubernetes.io/docs/tasks/debug-application-cluster/audit/). 

### What does this mean?

Well if looking at Google Clouds Offering that has this enabled by default you are able to keep a history of all the resources a user interacts with

```
{
  "protoPayload": {...},
  "requestMetadata": {...},
  "resourceName": "apps/v1/namespaces/default/deployments/nginx",
  "response": {...},
  "insertId": "f08533fb-6e56-4d7b-ae86-c4de88cda1e0",
  "resource": {
    "type": "k8s_cluster",
    "labels": {...}
  },
  "timestamp": "2021-03-11T12:48:09.307195Z",
  "labels": {
    "authorization.k8s.io/reason": "RBAC: allowed by RoleBinding \"namespace-editors/default\" of ClusterRole \"edit\" to User \"brendankamp75@gmail.com\"",
    "authorization.k8s.io/decision": "allow"
  },
  "logName": "name",
  "operation": {...},
  "receiveTimestamp": "2021-03-11T12:48:42.261916153Z"
}
```

### Implications

For PCI tracking what a user is doing is paramount as if any breach happens you will need to have the ability to track who made what changes that lead up to the breach.

## GitOps

Another way of handeling change management to a system is ofcourse GitOps which is the practice of using automation and Git Workflows to manage your applications and/or infrastructure.

### How does this help?

If we restrict changes to be done through Git Workflows by default we get a very concise history of who made which changes and why (commits) for example:

```bash
* 59f20af 2021-03-08 | SCaled Application to 3 for HA [Spazzy] (HEAD -> refs/heads/main, refs/remotes/origin/main, refs/remotes/origin/HEAD)
* bd5a61d 2021-03-08 | Scaled Cluster to 5 [Spazzy]
* 2aefcc3 2021-03-08 | Added Namespace for PCI Compliance [Spazzy]
* e5bcdfd 2021-03-08 | Updated the application to the latest version [Spazzy]
* 08f2055 2021-03-05 | Initial commit [Brendan Kamp]
```

This is ofcourse a super simplified version of what you could potentially get with this kind of setup
