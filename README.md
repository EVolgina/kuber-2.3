# Задание 1. Создать Deployment приложения и решить возникшую проблему с помощью ConfigMap. Добавить веб-страницу
- Создать Deployment приложения, состоящего из контейнеров nginx и multitool.[deployment](https://github.com/EVolgina/kuber-2.3/blob/main/deployment.yaml). [service](https://github.com/EVolgina/kuber-2.3/blob/main/service.yaml). [configmap](https://github.com/EVolgina/kuber-2.3/blob/main/cm.yaml)
- Решить возникшую проблему с помощью ConfigMap.
- Продемонстрировать, что pod стартовал и оба конейнера работают.
- Сделать простую веб-страницу и подключить её к Nginx с помощью ConfigMap. Подключить Service и показать вывод curl или в браузере.[webpage]()
- Предоставить манифесты, а также скриншоты или вывод необходимых команд.
```
vagrant@vagrant:~/kube/zd8$ kubectl get deployment
NAME              READY   UP-TO-DATE   AVAILABLE   AGE
nginx-multitool   0/1     1            0           79s
vagrant@vagrant:~/kube/zd8$ kubectl get service
NAME              TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)             AGE
nginx-multitool   ClusterIP   10.152.183.116   <none>        80/TCP              100s
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
vagrant@vagrant:~/kube/zd8$ kubectl describe pod nginx-multitool-54c6db7d88-zf48t
Name:             nginx-multitool-54c6db7d88-zf48t
Namespace:        default
Priority:         0
Service Account:  default
Node:             vagrant/10.0.2.15
Start Time:       Sat, 23 Mar 2024 07:50:33 +0000
Labels:           app=nginx-multitool
                  pod-template-hash=54c6db7d88
Annotations:      cni.projectcalico.org/containerID: 54ae0e88488a55f18b45021e8ccd412d78d1eb8cc74289233624bc08a055496a
                  cni.projectcalico.org/podIP: 10.1.52.173/32
                  cni.projectcalico.org/podIPs: 10.1.52.173/32
Status:           Running
IP:               10.1.52.173
IPs:
  IP:           10.1.52.173
Controlled By:  ReplicaSet/nginx-multitool-54c6db7d88
Containers:
  nginx:
    Container ID:   containerd://615ca471abeaafc70faa5739cc27f7474adf4440b7493e7206547816f0abb8f5
    Image:          nginx:latest
    Image ID:       docker.io/library/nginx@sha256:6db391d1c0cfb30588ba0bf72ea999404f2764febf0f1f196acd5867ac7efa7e
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Sat, 23 Mar 2024 07:50:41 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-xbd9c (ro)
  multitool:
    Container ID:   containerd://6e92670081f5854a68a18bb031391341e895827651135182906316aee6ef7db1
    Image:          wbitt/network-multitool
    Image ID:       docker.io/wbitt/network-multitool@sha256:d1137e87af76ee15cd0b3d4c7e2fcd111ffbd510ccd0af076fc98dddfc50a735
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Sat, 23 Mar 2024 07:51:44 +0000
    Last State:     Terminated
      Reason:       Error
      Exit Code:    1
      Started:      Sat, 23 Mar 2024 07:51:14 +0000
      Finished:     Sat, 23 Mar 2024 07:51:17 +0000
    Ready:          True
    Restart Count:  3
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-xbd9c (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  kube-api-access-xbd9c:
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
  Type     Reason     Age                From               Message
  ----     ------     ----               ----               -------
  Normal   Scheduled  73s                default-scheduler  Successfully assigned default/nginx-multitool-54c6db7d88-zf48t to vagrant
  Normal   Pulling    69s                kubelet            Pulling image "nginx:latest"
  Normal   Pulled     66s                kubelet            Successfully pulled image "nginx:latest" in 2.387s (2.387s including waiting)
  Normal   Created    66s                kubelet            Created container nginx
  Normal   Started    65s                kubelet            Started container nginx
  Normal   Pulled     62s                kubelet            Successfully pulled image "wbitt/network-multitool" in 2.311s (2.311s including waiting)
  Normal   Pulled     54s                kubelet            Successfully pulled image "wbitt/network-multitool" in 2.878s (2.878s including waiting)
  Normal   Pulled     33s                kubelet            Successfully pulled image "wbitt/network-multitool" in 2.333s (2.333s including waiting)
  Warning  BackOff    16s (x3 over 49s)  kubelet            Back-off restarting failed container multitool in pod nginx-multitool-54c6db7d88-zf48t_default(2f4fb8f4-187d-4ebf-95b2-cb49b714f039)
  Normal   Pulling    5s (x4 over 65s)   kubelet            Pulling image "wbitt/network-multitool"
  Normal   Pulled     3s                 kubelet            Successfully pulled image "wbitt/network-multitool" in 2.166s (2.166s including waiting)
  Normal   Created    2s (x4 over 62s)   kubelet            Created container multitool
  Normal   Started    2s (x4 over 61s)   kubelet            Started container multitool
vagrant@vagrant:~/kube/zd8$ sudo nano webpage.html
vagrant@vagrant:~/kube/zd8$ vagrant@vagrant:~/kube/zd8$ kubectl create configmap webpage --from-file=webpage.html
configmap/webpage created
```

  
# Задание 2. Создать приложение с вашей веб-страницей, доступной по HTTPS
- Создать Deployment приложения, состоящего из Nginx.
- Создать собственную веб-страницу и подключить её как ConfigMap к приложению.
- Выпустить самоподписной сертификат SSL. Создать Secret для использования сертификата.
- Создать Ingress и необходимый Service, подключить к нему SSL в вид. Продемонстировать доступ к приложению по HTTPS.
- Предоставить манифесты, а также скриншоты или вывод необходимых команд.
