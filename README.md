# Задание 1. Создать Deployment приложения и решить возникшую проблему с помощью ConfigMap. Добавить веб-страницу
- Создать Deployment приложения, состоящего из контейнеров nginx и multitool.[deployment](https://github.com/EVolgina/kuber-2.3/blob/main/deployment.yaml). [service](https://github.com/EVolgina/kuber-2.3/blob/main/service.yaml)
- Решить возникшую проблему с помощью ConfigMap.[configmap-nginx](https://github.com/EVolgina/kuber-2.3/blob/main/ng.yaml)
- Продемонстрировать, что pod стартовал и оба конейнера работают.
- Сделать простую веб-страницу и подключить её к Nginx с помощью ConfigMap. Подключить Service и показать вывод curl или в браузере.
- Предоставить манифесты, а также скриншоты или вывод необходимых команд.
```
vagrant@vagrant:~/kube/zd8$ kubectl get service
NAME             TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)             AGE
kubernetes       ClusterIP   10.152.183.1     <none>        443/TCP             48d
my-app-service   ClusterIP   10.152.183.230   <none>        9001/TCP,9002/TCP   19s
vagrant@vagrant:~/kube/zd8$ kubectl get deployment
NAME                READY   UP-TO-DATE   AVAILABLE   AGE
my-app-deployment   0/1     1            0           52s
vagrant@vagrant:~/kube/zd8$ kubectl get pods
NAME                                 READY   STATUS    RESTARTS      AGE
daemonset-5hmlm                      1/1     Running   2 (29m ago)   9d
my-app-deployment-8579458565-jjmmn   1/2     Error     2 (40s ago)   62s
-вносим изменения в deployment добавляем --
volumeMounts:
 - name: nginx-config
   mountPath: /etc/nginx/conf.d
volumes:
 - name: nginx-config
  configMap:
  name: nginx-config
- перзапускаем deployment и создаем configmap
vagrant@vagrant:~/kube/zd8$ kubectl get configmap
NAME               DATA   AGE
kube-root-ca.crt   1      48d
nginx-config       1      2m26s
vagrant@vagrant:~/kube/zd8$ kubectl get pods
NAME                               READY   STATUS        RESTARTS   AGE
daemonset-5hmlm                    1/1     Running       1          8d
nginx-multitool-54c6db7d88-vlg7n   2/2     Running       0          14s
nginx-multitool-585766f6b-gdprm    1/2     Terminating   0          7m35s
vagrant@vagrant:~/kube/zd8$ kubectl get pods
NAME                               READY   STATUS    RESTARTS      AGE
daemonset-5hmlm                    1/1     Running   1             8d
nginx-multitool-54c6db7d88-zf48t   2/2     Running   2 (20s ago)   44s
vagrant@vagrant:~/kube/zd8$ kubectl describe pod my-app-deployment-68f6cc7cf8-xmz26
Name:             my-app-deployment-68f6cc7cf8-xmz26
Namespace:        default
Priority:         0
Service Account:  default
Node:             vagrant/10.0.2.15
Start Time:       Sun, 24 Mar 2024 06:13:13 +0000
Labels:           app=my-app
                  pod-template-hash=68f6cc7cf8
Annotations:      cni.projectcalico.org/containerID: f73cc0f878c6d0188a11a5a895898196ca440c6d6c1f3c0606b9c088f2bf6259
                  cni.projectcalico.org/podIP: 10.1.52.143/32
                  cni.projectcalico.org/podIPs: 10.1.52.143/32
Status:           Running
IP:               10.1.52.143
IPs:
  IP:           10.1.52.143
Controlled By:  ReplicaSet/my-app-deployment-68f6cc7cf8
Containers:
  nginx:
    Container ID:   containerd://8e264c4f6197ba34f2e2ea56e62b2c68197ead7cf56ffd727876486a3738ce12
    Image:          nginx:latest
    Image ID:       docker.io/library/nginx@sha256:6db391d1c0cfb30588ba0bf72ea999404f2764febf0f1f196acd5867ac7efa7e
    Port:           8081/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Sun, 24 Mar 2024 06:13:22 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /etc/nginx/conf.d from nginx-config (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-jczz6 (ro)
  multitool:
    Container ID:   containerd://3ee5d6bce8181e2303b4ed9318bd3af4f02802e253f8ea3235b3db2d5593c103
    Image:          wbitt/network-multitool
    Image ID:       docker.io/wbitt/network-multitool@sha256:d1137e87af76ee15cd0b3d4c7e2fcd111ffbd510ccd0af076fc98dddfc50a735
    Port:           8080/TCP
    Host Port:      0/TCP
    State:          Waiting
      Reason:       CrashLoopBackOff
    Last State:     Terminated
      Reason:       Error
      Exit Code:    1
      Started:      Sun, 24 Mar 2024 06:14:27 +0000
      Finished:     Sun, 24 Mar 2024 06:14:30 +0000
    Ready:          False
    Restart Count:  3
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-jczz6 (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             False
  ContainersReady   False
  PodScheduled      True
Volumes:
  nginx-config:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      nginx-config
    Optional:  false
  kube-api-access-jczz6:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason     Age                 From               Message
  ----     ------     ----                ----               -------
  Normal   Scheduled  117s                default-scheduler  Successfully assigned default/my-app-deployment-68f6cc7cf8-xmz26 to vagrant
  Normal   Pulling    114s                kubelet            Pulling image "nginx:latest"
  Normal   Pulled     109s                kubelet            Successfully pulled image "nginx:latest" in 5.613s (5.614s including waiting)
  Normal   Created    108s                kubelet            Created container nginx
  Normal   Started    108s                kubelet            Started container nginx
  Normal   Pulled     106s                kubelet            Successfully pulled image "wbitt/network-multitool" in 1.828s (1.828s including waiting)
  Normal   Pulled     97s                 kubelet            Successfully pulled image "wbitt/network-multitool" in 3.025s (3.025s including waiting)
  Normal   Pulled     78s                 kubelet            Successfully pulled image "wbitt/network-multitool" in 2.549s (2.549s including waiting)
  Normal   Pulling    46s (x4 over 108s)  kubelet            Pulling image "wbitt/network-multitool"
  Normal   Pulled     44s                 kubelet            Successfully pulled image "wbitt/network-multitool" in 1.826s (1.826s including waiting)
  Normal   Created    44s (x4 over 106s)  kubelet            Created container multitool
  Normal   Started    43s (x4 over 105s)  kubelet            Started container multitool
  Warning  BackOff    27s (x5 over 92s)   kubelet            Back-off restarting failed container multitool in pod my-app-deployment-68f6cc7cf8-xmz26_default(1d58c99d-6503-4374-aa52-454fa673a070)
```
- создаем новый configmap. deployment. webpage.html все перезапускаем [cm.yaml](https://github.com/EVolgina/kuber-2.3/blob/main/cm.yaml). [webpage](https://github.com/EVolgina/kuber-2.3/blob/main/webpage.html). [deployment1](https://github.com/EVolgina/kuber-2.3/blob/main/deployment1.yaml)
```
vagrant@vagrant:~/kube/zd8$ kubectl get configmap
NAME               DATA   AGE
kube-root-ca.crt   1      48d
nginx-config       2      32m
my-website         1      22s
vagrant@vagrant:~/kube/zd8$ kubectl get pods
NAME                                 READY   STATUS    RESTARTS      AGE
daemonset-5hmlm                      1/1     Running   2 (93m ago)   9d
my-app-deployment-68f6cc7cf8-xmz26   2/2     Running   3 (35s ago)   78s
vagrant@vagrant:~/kube/zd8$ curl http://10.1.52.143:8081
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
vagrant@vagrant:~/kube/zd8$ kubectl get service
NAME             TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)             AGE
kubernetes       ClusterIP   10.152.183.1     <none>        443/TCP             48d
my-app-service   ClusterIP   10.152.183.230   <none>        9001/TCP,9002/TCP   103m
vagrant@vagrant:~/kube/zd8$ kubectl get pods -o wide
NAME                                 READY   STATUS    RESTARTS       AGE     IP            NODE      NOMINATED NODE   READINESS GATES
daemonset-5hmlm                      1/1     Running   2 (132m ago)   9d      10.1.52.162   vagrant   <none>           <none>
my-app-deployment-86b4d6c578-td4rk   2/2     Running   0              5m13s   10.1.52.148   vagrant   <none>           <none>
vagrant@vagrant:~/kube/zd8$ curl http://10.1.52.148:8081
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Welcome to my website</title>
</head>
<body>
    <h1>Hello from my website!</h1>
    <p>This is a simple webpage served by nginx.</p>
</body>
</html>
vagrant@vagrant:~/kube/zd8$ curl http://10.152.183.230:9001
WBITT Network MultiTool (with NGINX) - my-app-deployment-68f6cc7cf8-pkqnb - 10.1.52.144 - HTTP: 80 , HTTPS: 443 . (Formerly praqma/network-multitool)
```
 
# Задание 2. Создать приложение с вашей веб-страницей, доступной по HTTPS
- Создать Deployment приложения, состоящего из Nginx.[deployment2](https://github.com/EVolgina/kuber-2.3/blob/main/deployment2.yaml)
- Создать собственную веб-страницу и подключить её как ConfigMap к приложению. 
- Выпустить самоподписной сертификат SSL. Создать Secret для использования сертификата.
- Создать Ingress и необходимый Service, подключить к нему SSL в вид. Продемонстировать доступ к приложению по HTTPS.[ingress](https://github.com/EVolgina/kuber-2.3/blob/main/ingress.yaml). [service](https://github.com/EVolgina/kuber-2.3/blob/main/nginx-service.yaml)
- Предоставить манифесты, а также скриншоты или вывод необходимых команд.

- оставляем  ConfigMap из 1 задания [cm.yaml](https://github.com/EVolgina/kuber-2.3/blob/main/cm.yaml)
- выпускаем сертификат
```
vagrant@vagrant:~/kube/zd8$ vagrant@vagrant:~/kube/zd8$ openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=my-nginx-service.default.svc"
Generating a RSA private key
.........................+++++
................................................+++++
writing new private key to 'tls.key'
-----
vagrant@vagrant:~/kube/zd8$ kubectl get service
NAME             TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)             AGE
kubernetes       ClusterIP   10.152.183.1     <none>        443/TCP             49d
my-app-service   ClusterIP   10.152.183.230   <none>        9001/TCP,9002/TCP   9h
nginx-service    ClusterIP   10.152.183.58    <none>        443/TCP             7h30m
vagrant@vagrant:~/kube/zd8$ kubectl get ingress
NAME            CLASS    HOSTS         ADDRESS     PORTS     AGE
ingress         <none>   *                         80        28d
nginx-ingress   public   netology.ru   127.0.0.1   80, 443   3h55m
vagrant@vagrant:~/kube/zd8$ kubectl get deployment
NAME                READY   UP-TO-DATE   AVAILABLE   AGE
my-app-deployment   1/1     1            1           9h
nginx-deployment    0/1     1            0           3h56m
vagrant@vagrant:~/kube/zd8$ kubectl get configmap
NAME               DATA   AGE
kube-root-ca.crt   1      49d
my-website         1      8h
nginx-config       2      9h
vagrant@vagrant:~/kube/zd8$ kubectl get pods
NAME                                 READY   STATUS    RESTARTS      AGE
daemonset-5hmlm                      1/1     Running   2 (10h ago)   9d
my-app-deployment-86b4d6c578-td4rk   2/2     Running   0             8h
nginx-deployment-6cc7bf97f9-nl82p    1/1     Running   0             29s
```
```
vagrant@vagrant:~/kube/zd8$ nslookup nginx-service
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
Name:   nginx-service
Address: 10.152.183.58
vagrant@vagrant:~/kube/zd8$ kubectl get ingresses
NAME            CLASS    HOSTS        ADDRESS     PORTS     AGE
ingress         <none>   *                        80        28d
nginx-ingress   public   mysite.com   127.0.0.1   80, 443   88s
vagrant@vagrant:~/kube/zd8$ nslookup mysite.com
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
Name:   mysite.com
Address: 10.1.52.191

vagrant@vagrant:~/kube/zd8$ dig mysite.com

; <<>> DiG 9.16.1-Ubuntu <<>> mysite.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 11262
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;mysite.com.                    IN      A

;; ANSWER SECTION:
mysite.com.             0       IN      A       10.1.52.191

;; Query time: 0 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
;; WHEN: Sun Mar 24 16:29:49 UTC 2024
;; MSG SIZE  rcvd: 55
```
