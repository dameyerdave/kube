# Minio

## Setup

&rarr; found [here](https://min.io/docs/minio/kubernetes/upstream/index.html)

```bash
kubectl apply -f minio-dev.yaml
kubectl port-forward pod/minio 9000 9090 -n minio-dev
```