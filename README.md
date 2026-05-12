## Домашнее задание по теме "Хранение в Kubernetes"

### Задание 1

1. Создал [Deployment из двух контейнеров](deployment.yml)
2. Применил деплоймент
3. describe pods:

```
alex@uxtu-note:~/Study/kuber5/kuber5$ kubectl describe pods vol-app
Name:             vol-app-5676f457f9-gx5dj
Namespace:        default
Priority:         0
Service Account:  default
Node:             uxtu-note/192.168.0.100
Start Time:       Mon, 11 May 2026 23:31:07 +0300
Labels:           app=vol-app
                  pod-template-hash=5676f457f9
Annotations:      cni.projectcalico.org/containerID: a5530e9aaa5ec242772c693e027d2b17efae1af6fa42a146f95ff3b07a520ed8
                  cni.projectcalico.org/podIP: 10.1.69.238/32
                  cni.projectcalico.org/podIPs: 10.1.69.238/32
Status:           Running
IP:               10.1.69.238
IPs:
  IP:           10.1.69.238
Controlled By:  ReplicaSet/vol-app-5676f457f9
Containers:
  bbox:
    Container ID:  containerd://d966a66adeebeab067d3204b09f535bb000b63f167d70c5f6a1309bb8ced6459
    Image:         busybox:latest
    Image ID:      docker.io/library/busybox@sha256:1487d0af5f52b4ba31c7e465126ee2123fe3f2305d638e7827681e7cf6c83d5e
    Port:          <none>
    Host Port:     <none>
    Command:
      sh
      -c
      until 1==2 ; do sleep 5; date >> /common/datefile.html; done
    State:          Running
      Started:      Mon, 11 May 2026 23:31:10 +0300
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /common from common-vol (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-dc8xr (ro)
  mtool:
    Container ID:   containerd://9661310f349a5d86d88b39937f351492dabfc90a1deea14d6dbb55ba25e604e1
    Image:          wbitt/network-multitool
    Image ID:       docker.io/wbitt/network-multitool@sha256:db2810fe2c8d36db074eab5d98fbf861c8ed55e0786d648d3477b3de9135632e
    Port:           8080/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Mon, 11 May 2026 23:31:11 +0300
    Ready:          True
    Restart Count:  0
    Environment:
      HTTP_PORT:  8080
    Mounts:
      /common from common-vol (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-dc8xr (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True
  Initialized                 True
  Ready                       True
  ContainersReady             True
  PodScheduled                True
Volumes:
  common-vol:
    Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:
    SizeLimit:  500Mi
  kube-api-access-dc8xr:
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
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  47s   default-scheduler  Successfully assigned default/vol-app-5676f457f9-gx5dj to uxtu-note
  Normal  Pulling    47s   kubelet            Pulling image "busybox:latest"
  Normal  Pulled     46s   kubelet            Successfully pulled image "busybox:latest" in 1.177s (1.177s including waiting). Image size: 2222002 bytes.
  Normal  Created    46s   kubelet            Created container: bbox
  Normal  Started    45s   kubelet            Started container bbox
  Normal  Pulling    45s   kubelet            Pulling image "wbitt/network-multitool"
  Normal  Pulled     44s   kubelet            Successfully pulled image "wbitt/network-multitool" in 1.041s (1.041s including waiting). Image size: 96718848 bytes.
  Normal  Created    44s   kubelet            Created container: mtool
  Normal  Started    44s   kubelet            Started container mtool
```

4. Подключился к multitool контейнеру и проследил за наполнением файла из общего вольюма:

```
lex@uxtu-note:~/Study/kuber5/kuber5$ kubectl exec -it vol-app-5676f457f9-ln6j7 --container mtool -- bash
vol-app-5676f457f9-ln6j7:/# ls -l /common
total 4
-rw-r--r--    1 root     root          1479 May 11 20:18 datefile.html
vol-app-5676f457f9-ln6j7:/# tail -f /common/datefile.html
Mon May 11 20:18:41 UTC 2026
Mon May 11 20:18:46 UTC 2026
Mon May 11 20:18:51 UTC 2026
Mon May 11 20:18:56 UTC 2026
Mon May 11 20:19:01 UTC 2026
Mon May 11 20:19:06 UTC 2026
Mon May 11 20:19:11 UTC 2026
Mon May 11 20:19:16 UTC 2026
Mon May 11 20:19:21 UTC 2026
Mon May 11 20:19:26 UTC 2026
Mon May 11 20:19:31 UTC 2026
Mon May 11 20:19:36 UTC 2026
Mon May 11 20:19:41 UTC 2026
```

### Задание 2

1. Создал [Deployment](task2/deployment.yml) из двух конетейнеров, использующих общий том из PersistentVolumeClaim my-pvc.
2. Создал и применил манифест [persistentVolume my-pv](task2/pv.yml)

```
alex@uxtu-note:~/Study/kuber5/kuber5/task2$ kubectl apply -f pv.yml
persistentvolume/my-pv configured
alex@uxtu-note:~/Study/kuber5/kuber5/task2$ kubectl get pv
NAME    CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM            STORAGECLASS   VOLUMEATTRIBUTESCLASS   REASON   AGE
my-pv   1Gi        RWO            Delete           Bound    default/my-pvc   sc-local       <unset>                          19m
```

Создал и применил манифест [PersistentVolumeClaim my-pvc](task2/pvc.yml)

```
alex@uxtu-note:~/Study/kuber5/kuber5/task2$ kubectl apply -f pvc.yml
persistentvolumeclaim/my-pvc created
alex@uxtu-note:~/Study/kuber5/kuber5/task2$ kubectl get pvc
NAME     STATUS   VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
my-pvc   Bound    my-pv    1Gi        RWO            sc-local       <unset>                 19m
```

3. Проверил доступность данных, записанных подом bbox в поде mtool

```
alex@uxtu-note:~/Study/kuber5/kuber5/task2$ kubectl exec -it vol-app-d4997cf6f-m9g6p --container mtool -- bash
vol-app-d4997cf6f-m9g6p:/# tail -f /common/datefile.html
Tue May 12 11:11:12 UTC 2026
Tue May 12 11:11:17 UTC 2026
Tue May 12 11:11:22 UTC 2026
Tue May 12 11:11:27 UTC 2026
Tue May 12 11:11:32 UTC 2026
Tue May 12 11:11:37 UTC 2026
Tue May 12 11:11:42 UTC 2026
Tue May 12 11:11:47 UTC 2026
Tue May 12 11:11:52 UTC 2026
Tue May 12 11:11:57 UTC 2026
Tue May 12 11:12:02 UTC 2026
^C
```

4. Удалил deployment и pvc и проверил состояние pv

```
alex@uxtu-note:~/Study/kuber5/kuber5/task2$ kubectl delete deploy vol-app
deployment.apps "vol-app" deleted
alex@uxtu-note:~/Study/kuber5/kuber5/task2$ kubectl delete pvc my-pvc
persistentvolumeclaim "my-pvc" deleted
alex@uxtu-note:~/Study/kuber5/kuber5/task2$ kubectl get pv
NAME    CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM            STORAGECLASS   VOLUMEATTRIBUTESCLASS   REASON   AGE
my-pv   1Gi        RWO            Delete           Failed   default/my-pvc   sc-local       <unset>                          23m
alex@uxtu-note:~/Study/kuber5/kuber5/task2$ kubectl describe  pv
Name:            my-pv
Labels:          <none>
Annotations:     pv.kubernetes.io/bound-by-controller: yes
Finalizers:      [kubernetes.io/pv-protection]
StorageClass:    sc-local
Status:          Failed
Claim:           default/my-pvc
Reclaim Policy:  Delete
Access Modes:    RWO
VolumeMode:      Filesystem
Capacity:        1Gi
Node Affinity:   <none>
Message:         host_path deleter only supports /tmp/.+ but received provided /home/alex/Study/kuber5/kuber5/task2/common
Source:
    Type:          HostPath (bare host directory volume)
    Path:          /home/alex/Study/kuber5/kuber5/task2/common
    HostPathType:
Events:
  Type     Reason              Age   From                         Message
  ----     ------              ----  ----                         -------
  Warning  VolumeFailedDelete  32s   persistentvolume-controller  host_path deleter only supports /tmp/.+ but received provided /home/alex/Study/kuber5/kuber5/task2/common
```

5. Проверил, что после удаления клайма и деплоя файл данных остался на локальной файловой системе

```
alex@uxtu-note:~/Study/kuber5/kuber5/task2$ ls -l ./common/
итого 8
-rw-r--r-- 1 root root 6206 мая 12 14:27 datefile.html
```

Удалил PV и проверил состояние файла данных на локальной файловой системе

```
alex@uxtu-note:~/Study/kuber5/kuber5/task2$ kubectl delete pv my-pv
persistentvolume "my-pv" deleted
alex@uxtu-note:~/Study/kuber5/kuber5/task2$ ls -l ./common/
итого 8
-rw-r--r-- 1 root root 6206 мая 12 14:27 datefile.html
```

После удаления PV файл данных по прежнему остался на месте, так как для HostPath удаление работает только с путями по регулярному выражению /tmp/.+.
