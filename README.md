# pci-compliant-namespace

A theoretical example of a multi tenant (or app) cluster with PCI Compliant namespaces that is able to host multiple apps in the same cluster

**NOTE: This is currently a theoretical example and will need to be verified**

## The Problem

When Approaching PCI Compliance a general pattern that is seen is that a company will create a fully seperate system that handles the PCI compliance. This is seen as the "safe method" as if nothing else is in the same place, there is generally less cause for concern. If we take into account Kubernetes this would generally be an entire Kubernetes cluster dedicated to a PCI compliant application. For any person or team that runs Kubernetes clusters at scale, the overhead of running a dedicated cluster might not be optimal, and comes with its own challenges, but what are the other choices?

### First lets start with what is PCI Compliance and why is it important?

The Payment Card Industry Data Security Standard (PCI DSS) is a set of requirements to standradise and ensure that all companies that process, store or transmit card data do so in a secure way. This requirement is governed over by an indpendant body that was created by Visa, MasterCard, American Express, Discover and JCB. In essence the requirements allow for the protection of personal payemnt information for oinline transactions.

### The Normal Way

Having a dedicated cluster might look something along the lines of this:

![sepearte environments](./images/seperate-environments.png)

Key Concerns when looking at this:

- There is a lot of duplicated infrastructure
- The idea behind having a "super secure" application and a normal application creates idea that the normal production enviornment should not be held up to as strict security requirements
- Maintaining and running dual envionrments create cognitive load on the teams responsible
- Deviation in standards can occuer if one environment is more actively developed on
