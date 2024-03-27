# Lesson 4 - Create the front-end deployment

1. File deployment.yaml

```bash
home/tungnd/devops-k8s/lesson-4# cat deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello
spec:
  template:
    metadata:
      labels:
        app: hello-demo
    spec:
      containers:
      - name: server
        image: nginx:1.17
  selector:
    matchLabels:
      app: hello-demo
  replicas: 2

```

1. Apply

```bash
/home/tungnd/devops-k8s/lesson-4# kubectl apply -f ./deployment.yaml
deployment.apps/hello created
```

1. Check

```bash
/home/tungnd/devops-k8s/lesson-4# kubectl get pods
NAME                                                     READY   STATUS             RESTARTS          AGE
argocd                                                   0/1     CrashLoopBackOff   124 (3m25s ago)   11h
argocd-application-controller-0                          1/1     Running            2 (48d ago)       145d
argocd-applicationset-controller-787bfd9669-ssk2t        1/1     Running            2 (48d ago)       145d
argocd-dex-server-bb76f899c-gqbqs                        1/1     Running            2 (48d ago)       145d
argocd-notifications-controller-5557f7bb5b-5l4r7         1/1     Running            2 (48d ago)       145d
argocd-redis-b5d6bf5f5-fhh8l                             1/1     Running            2 (48d ago)       145d
argocd-repo-server-56998dcf9c-lgzfw                      1/1     Running            2 (48d ago)       145d
argocd-server-5985b6cf6f-mg77t                           1/1     Running            2 (48d ago)       145d
hello-6996c677cf-47wsx                                   1/1     Running            0                 17s
hello-6996c677cf-xhxjv                                   1/1     Running            0                 17s
nginx-deployment-57d84f57dc-9srbk                        1/1     Running            0                 14d
nginx-deployment-57d84f57dc-fwxcb                        1/1     Running            0                 14d
nginx-ingress-ingress-nginx-controller-d776646fd-p7vwm   1/1     Running            0                 12d
nginx-proxy-549756b896-4249n                             1/1     Running            0                 12d
toolbox                                                  1/1     Running            0                 11h         0                 13d

```

```bash
/home/tungnd/devops-k8s/lesson-4# kubectl exec -it hello-6996c677cf-47wsx bash
kubectl exec [POD] [COMMAND] is DEPRECATED and will be removed in a future version. Use kubectl exec [POD] -- [COMMAND] instead.
root@hello-6996c677cf-47wsx:/# service nginx status
[ ok ] nginx is running.
root@hello-6996c677cf-47wsx:/# exit
exit

```

1. Scale up 

```bash
home/tungnd/devops-k8s/lesson-4# kubectl scale deployment/hello --replicas=10
deployment.apps/hello scaled
/home/tungnd/devops-k8s/lesson-4# kubectl get pods
NAME                                                     READY   STATUS              RESTARTS          AGE
hello-6996c677cf-47wsx                                   1/1     Running             0                 2m8s
hello-6996c677cf-67jgt                                   0/1     ContainerCreating   0                 4s
hello-6996c677cf-b47kz                                   0/1     ContainerCreating   0                 4s
hello-6996c677cf-hjjpn                                   0/1     ContainerCreating   0                 4s
hello-6996c677cf-jmx8f                                   0/1     ContainerCreating   0                 4s
hello-6996c677cf-pbv7t                                   1/1     Running             0                 4s
hello-6996c677cf-qxtfb                                   1/1     Running             0                 4s
hello-6996c677cf-twwhc                                   0/1     ContainerCreating   0                 4s
hello-6996c677cf-w5855                                   0/1     ContainerCreating   0                 4s
hello-6996c677cf-xhxjv                                   1/1     Running             0                 2m8s

```

1. Scale down 

```bash
/home/tungnd/devops-k8s/lesson-4# kubectl scale deployment/hello --replicas=4
deployment.apps/hello scaled
/home/tungnd/devops-k8s/lesson-4# kubectl get pods
NAME                                                     READY   STATUS             RESTARTS          AGE
hello-6996c677cf-hjjpn                                   1/1     Running            0                 3h35m
hello-6996c677cf-jmx8f                                   1/1     Running            0                 3h35m
hello-6996c677cf-pbv7t                                   1/1     Running            0                 3h35m
hello-6996c677cf-w5855                                   1/1     Running            0                 3h35m

```

1. Delete pods

```bash
home/tungnd/devops-k8s/lesson-4# kubectl delete pod hello-6996c677cf-hjjpn
pod "hello-6996c677cf-hjjpn" deleted
```

1. Edit file deployment

![Untitled](Lesson%204%20-%20Create%20the%20front-end%20deployment%204ef5f95ef25e449eb620f07995d98ee9/Untitled.png)

1. Apply

```bash
/home/tungnd/devops-k8s/lesson-4# kubectl apply -f ./deployment.yaml
deployment.apps/k8scourse-front created
/home/tungnd/devops-k8s/lesson-4# kubectl get pods
NAME                                                     READY   STATUS              RESTARTS          AGE
k8scourse-front-6b8458f74-vqjw5                          0/1     ContainerCreating   0                 16s
k8scourse-front-6b8458f74-w4cdv                          1/1     Running             0                 16s
toolbox                                                  1/1     Running             0                 16h

```

# Lesson 5 - Create the front end service

1. Check pods

```bash
/home/tungnd/devops-k8s# kubectl get pods -l role=frontend
NAME                              READY   STATUS    RESTARTS   AGE
k8scourse-front-6b8458f74-vqjw5   1/1     Running   0          3m39s
k8scourse-front-6b8458f74-w4cdv   1/1     Running   0          3m39s

```

1. Create apply service

```bash
home/tungnd/devops-k8s/lesson-5# cat service.yaml
apiVersion: v1
kind: Service
metadata:
  name: k8scourse-front
  labels:
    role: frontend
spec:
  selector:
    role: frontend
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080

home/tungnd/devops-k8s/lesson-5# kubectl apply -f ./service.yaml
service/k8scourse-front created
```

1. Delete

```bash
/home/tungnd/devops-k8s/lesson-5# kubectl delete pods -l role=frontend
pod "k8scourse-front-6b8458f74-vqjw5" deleted
pod "k8scourse-front-6b8458f74-w4cdv" deleted
/home/tungnd/devops-k8s/lesson-5# kubectl get pods
NAME                                                     READY   STATUS             RESTARTS        AGE
k8scourse-front-6b8458f74-jjqrv                          1/1     Running            0               2m16s
k8scourse-front-6b8458f74-rmqxp                          1/1     Running            0               2m16s

```

```bash
home/tungnd/devops-k8s/lesson-5# kubectl exec k8scourse-front-6b8458f74-jjqrv -- printenv
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=k8scourse-front-6b8458f74-jjqrv
ARGOCD_SERVER_PORT_443_TCP=tcp://10.43.102.116:443
ARGOCD_DEX_SERVER_PORT=tcp://10.43.240.30:5556
ARGOCD_DEX_SERVER_PORT_5556_TCP_ADDR=10.43.240.30
ARGOCD_DEX_SERVER_PORT_5558_TCP_PROTO=tcp
ARGOCD_SERVER_METRICS_SERVICE_HOST=10.43.120.193
KUBERNETES_PORT_443_TCP=tcp://10.43.0.1:443

/home/tungnd/devops-k8s/lesson-5# kubectl delete pod toolbox
pod "toolbox" deleted
/home/tungnd/devops-k8s/lesson-3# kubectl apply -f ./toolbox.json
pod/toolbox created
/home/tungnd/devops-k8s/lesson-3# kubectl exec -it toolbox -- /bin/bash
[root@toolbox /]# curl 10.96.106.163:80
<!DOCTYPE html><html lang=en><head><meta charset=utf-8><meta http-equiv=X-UA-Compatible content="IE=edge"><meta name=viewport content="width=device-width,initial-scale=1"><link rel=icon href=/favicon.ico><title>front</title><link href=/css/app.1e8d7171.css rel=preload as=style><link href=/css/chunk-vendors.c3958256.css rel=preload 
as=style><link href=/js/app.513221f3.js rel=preload as=script><link href=/js/chunk-vendors.b134dd4f.js rel=preload as=script><link href=/css/chunk-vendors.c3958256.css rel=stylesheet><link href=/css/app.1e8d7171.css rel=stylesheet></head><body><noscript><strong>We're sorry but front doesn't work properly without JavaScript enabled.
  Please enable it to continue.</strong></noscript><div id=app></div><script src=/js/chunk-vendors.b134dd4f.js></script><script src=/js/app.513221f3.js></script></body></html>[root@toolbox /]#
[root@toolbox /]# curl k8scourse-front
<!DOCTYPE html><html lang=en><head><meta charset=utf-8><meta http-equiv=X-UA-Compatible content="IE=edge"><meta name=viewport content="width=device-width,initial-scale=1"><link rel=icon href=/favicon.ico><title>front</title><link href=/css/app.1e8d7171.css rel=preload as=style><link href=/css/chunk-vendors.c3958256.css rel=preload 
as=style><link href=/js/app.513221f3.js rel=preload as=script><link href=/js/chunk-vendors.b134dd4f.js rel=preload as=script><link href=/css/chunk-vendors.c3958256.css rel=stylesheet><link href=/css/app.1e8d7171.css rel=stylesheet></head><body><noscript><strong>We're sorry but front doesn't work properly without JavaScript enabled.
 Please enable it to continue.</strong></noscript><div id=app></div><script src=/js/chunk-vendors.b134dd4f.js></script><script src=/js/app.513221f3.js></script></body></html>[root@toolbox /]#

```