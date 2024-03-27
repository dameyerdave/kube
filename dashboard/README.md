# Kubernetes dashboard

## Deployment

&rarr; [https://upcloud.com/resources/tutorials/deploy-kubernetes-dashboard](https://upcloud.com/resources/tutorials/deploy-kubernetes-dashboard)

Deploy the dashboard.

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
```

Deploy the admin role.

```bash
kubectl apply -f dashboard/dashboard-admin.yaml
```

Get the admin token.

```bash
kubectl -n kubernetes-dashboard create token admin-user
```

Create a long-lived bearer token.

```bash
kubectl apply -f dashboard/long-lived-bearer-token.yaml
kubectl get secret admin-user -n kubernetes-dashboard -o jsonpath={".data.token"} | base64 -d
```

Deploy the servide.

```bash
kubectl apply -f dashboard/dashboard-service.yaml
```