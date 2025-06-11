# k8s-PCI

![Phorge logo](https://avatars.githubusercontent.com/u/187407936?s=200&v=4)

Core K8S is the main k8s cluster running @ Phorge !

## Setup

requirements:

- [Ansible-PCI](https://github.com/phorge-fr/Ansible-PCI)
- [FluxCD](https://fluxcd.io/)
- [Mozilla SOPS](https://getsops.io/)

1. Deploy cluster

```bash
ansible-playbook collections/ansible_collections/techno_tim/k3s_ansible/site.yml
```

2. Run post install setup

```bash
ansible-playbook playbooks/k3s-post-install.yml
```

3. Get kubeconfig

```
cat collections/ansible_collections/techno_tim/k3s_ansible/kubeconfig > ~/.kube/config
```

4. Create sops key for the cluster

```bash
age-keygen -o age.agekey
kubectl create namespace flux-system
cat age.agekey |
kubectl create secret generic sops-age \
--namespace=flux-system \
--from-file=age.agekey=/dev/stdin
rm age.agekey
```

5. Bootstrap flux

```bash
flux bootstrap github --owner=phorge-fr --repository=k8s-PCI --branch=main --path=cluster/ControlPlane
```
