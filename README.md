# introduction

Tutorial for develop Django with Kubernetes following the article from [Digital Ocean](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-scalable-and-secure-django-application-with-kubernetes)

# login in registry

```
$ doctl registry login
$ docker tag <my-image> registry.digitalocean.com/<my-registry>/<my-image>
$ docker push registry.digitalocean.com/<my-registry>/<my-image>
```

# kubernetes

```
$ doctl kubernetes cluster list
$ doctl kubernetes cluster kubeconfig save <my-cluster-id>
$ doctl registry kubernetes-manifest | kubectl apply -f -
$ kubectl patch serviceaccount default -p '{"imagePullSecrets": [{"name": "registry-<my-registry>"}]}'
```

# applying yamls

```
$ kubectl apply -f yaml/polls-configmap.yaml
$ kubectl create secret generic polls-secret --from-env-file=yaml/.polls-secrets
$ kubectl apply -f yaml/polls-deployment.yaml
$ kubectl apply -f yaml/polls-svc.yaml
```

## ingress

```
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.34.1/deploy/static/provider/do/deploy.yaml
```

## cert manager

```
$ kubectl apply --validate=false -f https://github.com/jetstack/cert-manager/releases/download/v0.16.1/cert-manager.yaml
$ kubectl create -f yaml/cert-staging-issuer.yaml
$ kubectl create -f yaml/cert-prod-issuer.yaml
```

## ingress (part two)

```
$ kubectl apply -f yaml/ingress-nginx-scv.yaml
$ kubectl apply -f yaml/polls-ingress.yaml
$ kubectl apply -f yaml/polls-svc.yaml --force
```

# checking items

```
$ kubectl describe secret polls-secret
$ kubectl get deploy polls-app
$ kubectl describe deploy
$ kubectl get pod
$ kubectl get svc polls
$ kubectl get node -o wide
```

## ingress

```
$ kubectl get pods -n ingress-nginx -l app.kubernetes.io/name=ingress-nginx --watch
$ kubectl get svc --namespace=ingress-nginx
```

## cert manager

```
$ kubectl get pods --namespace cert-manager
```

## ingress (part two)

```
$ kubectl describe ingress polls-ingress
$ wget -O - http://polls.dochaus.app/polls
```