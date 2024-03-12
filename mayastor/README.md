# Mayastor

## Preparation

&rarr; [[here](https://mayastor.gitbook.io/introduction/quickstart/prerequisites)](https://mayastor.gitbook.io/introduction/quickstart/prerequisites)

Enable Huge Page Support.

```bash
echo 1024 | sudo tee /sys/kernel/mm/hugepages/hugepages-2048kB/nr_hugepages
echo vm.nr_hugepages = 1024 | sudo tee -a /etc/sysctl.conf
grep HugePages /proc/meminfo
```

Label Mayastor Node Candidates.

```bash
kubectl label node <node_name> openebs.io/engine=mayastor
```

## Deployment

&rarr; [https://mayastor.gitbook.io/introduction/quickstart/deploy-mayastor](https://mayastor.gitbook.io/introduction/quickstart/deploy-mayastor)

```bash
helm repo add mayastor https://openebs.github.io/mayastor-extensions/
MAYASTOR_VERSION=$(helm search repo mayastor --versions | head -2 | tail -1 | awk '{print $2}')
helm install mayastor mayastor/mayastor -n mayastor --create-namespace --version ${MAYASTOR_VERSION}
kubectl get pods -n mayastor
```

## Configuration

