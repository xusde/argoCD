## Setup ArgoCD in Minikube

Note: You can setup Argo CD in any cluster, instructions are same. 

- First install Minikube:
    Installation guide for installing Minikube. 
    [Minikube.sigs.k8s.io](https://minikube.sigs.k8s.io/docs/start/?arch=%2Fwindows%2Fx86-64%2Fstable%2F.exe+download)

---
- Install Argo CD
    ```
    kubectl create namespace argocd
    kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml 
    ```

- Verify if ArgoCD is running:
    ```
    kubectl get all -n argocd
    ```
    Output
    ```    
        NAME                                                    READY   STATUS    RESTARTS       AGE
    pod/argocd-application-controller-0                     1/1     Running   0              2m57s
    pod/argocd-applicationset-controller-64f6bd6456-6jv2z   1/1     Running   0              2m57s
    pod/argocd-dex-server-5fdcd9df8b-6ctpr                  1/1     Running   1 (2m2s ago)   2m57s
    pod/argocd-notifications-controller-778495d96f-rhj9k    1/1     Running   0              2m57s
    pod/argocd-redis-69fd8bd669-5cwkf                       1/1     Running   0              2m57s
    pod/argocd-repo-server-75567c944-mth62                  1/1     Running   0              2m57s
    pod/argocd-server-5c768cdd96-6rpdp                      1/1     Running   0              2m57s

    NAME                                              TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
    service/argocd-applicationset-controller          ClusterIP   10.96.214.139    <none>        7000/TCP,8080/TCP            2m57s
    service/argocd-dex-server                         ClusterIP   10.105.242.131   <none>        5556/TCP,5557/TCP,5558/TCP   2m57s
    service/argocd-metrics                            ClusterIP   10.108.182.252   <none>        8082/TCP                     2m57s
    service/argocd-notifications-controller-metrics   ClusterIP   10.106.4.82      <none>        9001/TCP                     2m57s
    service/argocd-redis                              ClusterIP   10.98.222.183    <none>        6379/TCP                     2m57s
    service/argocd-repo-server                        ClusterIP   10.103.237.141   <none>        8081/TCP,8084/TCP            2m57s
    service/argocd-server                             ClusterIP   10.107.245.182   <none>        80/TCP,443/TCP               2m57s
    service/argocd-server-metrics                     ClusterIP   10.108.248.213   <none>        8083/TCP                     2m57s

    NAME                                               READY   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/argocd-applicationset-controller   1/1     1            1           2m57s
    deployment.apps/argocd-dex-server                  1/1     1            1           2m57s
    deployment.apps/argocd-notifications-controller    1/1     1            1           2m57s
    deployment.apps/argocd-redis                       1/1     1            1           2m57s
    deployment.apps/argocd-repo-server                 1/1     1            1           2m57s
    deployment.apps/argocd-server                      1/1     1            1           2m57s

    NAME                                                          DESIRED   CURRENT   READY   AGE
    replicaset.apps/argocd-applicationset-controller-64f6bd6456   1         1         1       2m57s
    replicaset.apps/argocd-dex-server-5fdcd9df8b                  1         1         1       2m57s
    replicaset.apps/argocd-notifications-controller-778495d96f    1         1         1       2m57s
    replicaset.apps/argocd-redis-69fd8bd669                       1         1         1       2m57s
    replicaset.apps/argocd-repo-server-75567c944                  1         1         1       2m57s
    replicaset.apps/argocd-server-5c768cdd96                      1         1         1       2m57s

    NAME                                             READY   AGE
    statefulset.apps/argocd-application-controller   1/1     2m57s
    ```

---
- Access ArgoCD With configuring NodePort 
    ```
    kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "NodePort"}}'
    ```
- Verify if ArgoCD server running as NodePort.
   ```
   kubectl get svc -n argocd
   ``` 
   Output
   ```
   NAME                                      TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
    argocd-applicationset-controller          ClusterIP   10.96.214.139    <none>        7000/TCP,8080/TCP            7m17s
    argocd-dex-server                         ClusterIP   10.105.242.131   <none>        5556/TCP,5557/TCP,5558/TCP   7m17s
    argocd-metrics                            ClusterIP   10.108.182.252   <none>        8082/TCP                     7m17s
    argocd-notifications-controller-metrics   ClusterIP   10.106.4.82      <none>        9001/TCP                     7m17s
    argocd-redis                              ClusterIP   10.98.222.183    <none>        6379/TCP                     7m17s
    argocd-repo-server                        ClusterIP   10.103.237.141   <none>        8081/TCP,8084/TCP            7m17s
    argocd-server                             NodePort    10.107.245.182   <none>        80:30692/TCP,443:31365/TCP   7m17s
    argocd-server-metrics                     ClusterIP   10.108.248.213   <none>        8083/TCP                     7m17s
   ```
- Grab ArgoCD secret for accessing UI
   ```
   kubectl get secrets -n argocd argocd-initial-admin-secret -o jsonpath='{.data.password}' | base64 -d
   ```

- Start Minkube Service. 
   ```
    minikube service argocd-server -n argocd
    ```
    Output
    ```
    |-----------|---------------|-------------|-----------------------------|
    | NAMESPACE |     NAME      | TARGET PORT |             URL             |
    |-----------|---------------|-------------|-----------------------------|
    | argocd    | argocd-server | http/80     | http://172.29.213.129:30692 |
    |           |               | https/443   | http://172.29.213.129:31365 |
    |-----------|---------------|-------------|-----------------------------|
    [argocd argocd-server http/80
    https/443 http://172.29.213.129:30692
    http://172.29.213.129:31365]

   ```
   Username: admin
   password: secret(please check above command)

   ![alt text](imgs/ui.png)

---
Setup our Continous deployment. 

- Select New App.
![alt text](imgs/setting1.png)
![alt text](imgs/setting2.png)
---
- Syncing your manifests files:
![alt text](imgs/sync.png)
---

- Successfully Deployed our app:
![alt text](imgs/deployed.png)
---
Access Application with below command.
```
minikube service list
```
Output
```
|-------------|-----------------------------------------|--------------|-----------------------------|
|  NAMESPACE  |                  NAME                   | TARGET PORT  |             URL             |
|-------------|-----------------------------------------|--------------|-----------------------------|
| argocd      | argocd-applicationset-controller        | No node port |                             |
| argocd      | argocd-dex-server                       | No node port |                             |
| argocd      | argocd-metrics                          | No node port |                             |
| argocd      | argocd-notifications-controller-metrics | No node port |                             |
| argocd      | argocd-redis                            | No node port |                             |
| argocd      | argocd-repo-server                      | No node port |                             |
| argocd      | argocd-server                           | http/80      | http://172.29.213.129:30692 |
|             |                                         | https/443    | http://172.29.213.129:31365 |
| argocd      | argocd-server-metrics                   | No node port |                             |
| default     | kubernetes                              | No node port |                             |
| default     | weather-check-service                   |         5000 | http://172.29.213.129:30008 |
| kube-system | kube-dns                                | No node port |                             |
|-------------|-----------------------------------------|--------------|-----------------------------|
```
---
Application running on http://172.29.213.129:30008

![alt text](imgs/application.png)