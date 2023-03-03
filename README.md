# SADA Ground School 2023: Governance and Compliance with Policy Controller

This repository contains demo materials presented at the _SADA Ground School: Governance and Compliance with Policy Controller_ session.

## Demo Steps

1. Create two GKE clusters. These can be created any way you like. If you want, create an Anthos multicloud cluster compatible with Policy Controller.

2. Install Policy Controller using the blue plus box at the top of the Google Cloud console page under "Anthos > Fleet Features > Policy".

3. Select the clusters to install Policy Controller. Click next and use the default installation options for Policy Controller. Click next and unselect "Enable Config sync". Config sync is not necessary for this demo, although beneficial for managing policy at scale.

4. Click complete!

5. Policy Controller will now install on your cluster. Consider installing a demo application, such as `Bank of Anthos` to see results against workloads. *Note that `Bank of Anthos` includes a publicly accessible load balancer that may violate your organization's security requirements.*

```sh
# install Bank of Anthos for sample workloads
kubectl apply -f ./bank-of-anthos/extras/jwt/jwt-secret.yaml
kubectl apply -f ./bank-of-anthos/kubernetes-manifests
```

6. Apply the CIS Kubernetes Benchmark bundle.

```sh
kubectl apply -k https://github.com/GoogleCloudPlatform/acm-policy-controller-library.git/bundles/cis-k8s-v1.5.1
```

7. View the policy bundle results in either the terminal or the Google Cloud console Policy Dashboard.

```sh
kubectl get constraint -l policycontroller.gke.io/bundleName=cis-k8s-v1.5.1 -o json | jq -cC '.items[]| [.metadata.name,.status.totalViolations]'
```

8. Create a custom policy that blocks namespaces without an owner label.

```
# create the constraint
kubectl apply -f namespace-should-have-owner-label.yaml

# try creating a violating namespace that will be blocked
kubectl apply -f bad-namespace.yaml

# now create a compliant namespace
kubectl apply -f good-namespace.yaml
```
