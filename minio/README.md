# Minio

## Setup

&rarr; found [here](https://min.io/docs/minio/kubernetes/upstream/index.html)

```bash
kubectl apply -f minio-dev.yaml
kubectl port-forward pod/minio 9000 9090 -n minio-dev
```

Minio is now available on `NodePort: 30090`. Default cretentials are `minioadmin | minioadmin`.

### Enable https

There must be the certificates in `/root/.minio/certs`. The certs can be generated using the following command:

```bash
openssl req -x509 -nodes -days 730 -newkey rsa:2048 -keyout /root/.minio/certs/private.key -out /root/.minio/certs/public.crt -config /root/.minio/certs/req.conf -extensions 'v3_req'
```