# ozby-challenge


## Test cluster
```
export GITHUB_PAT=$(cat ~/.github/pat)

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
name: webhook-secret
namespace: challenge
stringData:
token: ${GITHUB_PAT}
secret: $(openssl rand -base64 12)
EOF
```

### Add your docker hub credentials to push image
```
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
name: docker-credentials
namespace: challenge
data:
config.json: $(cat ~/.docker/config.json | base64 -w0)
EOF
```


### Add the app to your ArgoCD or install as helm chart
```
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: ozby-challenge
  namespace: argo-cd
spec:
    project: default
    source:
        repo-url: 'https://github.com/ozby/ozby-challenge.git'
        path: chart
        targetRevision: HEAD
        helm:
            valueFiles:
                - values.yaml
    destination:
        server: 'https://kubernetes.production.svc'
        namespace: ozby-challenge
    syncPolicy:
        automated:
        prune: true
        syncOptions:
        - CreateNamespace=true
```


# challenge
