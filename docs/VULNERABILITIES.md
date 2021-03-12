# Vulnerabilities

Scanning software for vulnerabilities shouild always be integrated in your security policies in some form or another, there is also the added extra value of runtime security that notifies system admins or blocks bad actors who have gotten access to a system. For PCI Compliance this is critical as Bad actors generally take advantage of software vulnerabilities to gain access.

## What does this look like in Kubernetes?

For our use case of making a namepsace PCI compliant this has two implications

### CVE scanning

There are currently many CVE scanning tools for containers such as [Trivy](https://github.com/aquasecurity/trivy) or [Clair](https://github.com/quay/clair) that can easily scan containers for CVE's (Common Vulnerabilities). For PCI use case it would make sens to do this at build time (When we are building our containers) and not allowing deployment if the scanners fail. However if this is done in an automation pipeline outside of the system there is always a chance that something can be deployed to a cluster that was not checked. This is a great example of where we can use image signing to verify that deployed images have been checked and use an [admission controller](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/) to not allow any copntainers that have not been signed. See [Notary Github](https://github.com/theupdateframework/notary) for how this could look

The workflow would look similar to:

![Sign Scanned Images](../images/SignScannedImages.png)

### Runtime Scanning

// TODO
