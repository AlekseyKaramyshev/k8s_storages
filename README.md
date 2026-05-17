
## Задание 1. Volume: обмен данными между контейнерами в поде
### Задача

Создать Deployment приложения, состоящего из двух контейнеров, обменивающихся данными.

### Шаги выполнения
1. Создать Deployment приложения, состоящего из контейнеров busybox и multitool.
2. Настроить busybox на запись данных каждые 5 секунд в некий файл в общей директории.
3. Обеспечить возможность чтения файла контейнером multitool.


### Что сдать на проверку
- Манифесты:
  - `containers-data-exchange.yaml`
- Скриншоты:
  - описание пода с контейнерами (`kubectl describe pods data-exchange`)
  - вывод команды чтения файла (`tail -f <имя общего файла>`)

------

## Решение

<img width="918" height="425" alt="1_1" src="https://github.com/user-attachments/assets/bd53fb91-b8d8-404b-b713-ab4f78a99afc" />

<img width="1041" height="730" alt="1_2" src="https://github.com/user-attachments/assets/be6ea88e-7596-4c0c-98c9-a5d9ae6661a9" />

<details>
<summary>microk8s kubectl describe pods data-exchange</summary>

```bash
Name:             data-exchange-9844858f7-jl52x
Namespace:        default
Priority:         0
Service Account:  default
Node:             vwffpktiuk/xx.xx.xx.xx
Start Time:       Sun, 17 May 2026 12:06:42 +0000
Labels:           app=data-exchange
                  pod-template-hash=9844858f7
Annotations:      cni.projectcalico.org/containerID: b3a57c7b37688112e59cddeed198b8fcc6e237a6173fac82dd84c0c7cbd52938
                  cni.projectcalico.org/podIP: 10.1.48.70/32
                  cni.projectcalico.org/podIPs: 10.1.48.70/32
Status:           Running
IP:               10.1.48.70
IPs:
  IP:           10.1.48.70
Controlled By:  ReplicaSet/data-exchange-9844858f7
Containers:
  busybox1:
    Container ID:  containerd://ce7eff3f9ef9aab9c445161e453233ec89da468e9a659f1319776ee951e213fd
    Image:         busybox
    Image ID:      docker.io/library/busybox@sha256:1487d0af5f52b4ba31c7e465126ee2123fe3f2305d638e7827681e7cf6c83d5e
    Port:          <none>
    Host Port:     <none>
    Command:
      /bin/sh
      -c
    Args:
      while true; do
        echo $(date) >> /shared-data/test_date.log
        sleep 5
      done
      
    State:          Running
      Started:      Sun, 17 May 2026 12:06:43 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /shared-data from shared-volume (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-kbj5z (ro)
  multitool:
    Container ID:  containerd://73a5019795cb8909ea9ea89903aeeb6278075b2439727bb4f78fcd8853b76c1a
    Image:         wbitt/network-multitool
    Image ID:      docker.io/wbitt/network-multitool@sha256:db2810fe2c8d36db074eab5d98fbf861c8ed55e0786d648d3477b3de9135632e
    Port:          <none>
    Host Port:     <none>
    Command:
      /bin/sh
      -c
    Args:
      tail -f /shared-data/test_date.log
    State:          Running
      Started:      Sun, 17 May 2026 12:06:44 +0000
    Ready:          False
    Restart Count:  0
    Readiness:      exec [test -f /shared-data/test-date.log] delay=10s timeout=1s period=5s #success=1 #failure=3
    Environment:    <none>
    Mounts:
      /shared-data from shared-volume (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-kbj5z (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       False 
  ContainersReady             False 
  PodScheduled                True 
Volumes:
  shared-volume:
    Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:     
    SizeLimit:  <unset>
  kube-api-access-kbj5z:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    Optional:                false
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason     Age                 From               Message
  ----     ------     ----                ----               -------
  Normal   Scheduled  2m2s                default-scheduler  Successfully assigned default/data-exchange-9844858f7-jl52x to vwffpktiuk
  Normal   Pulling    2m2s                kubelet            Pulling image "busybox"
  Normal   Pulled     2m1s                kubelet            Successfully pulled image "busybox" in 901ms (901ms including waiting). Image size: 2222002 bytes.
  Normal   Created    2m1s                kubelet            Created container: busybox1
  Normal   Started    2m1s                kubelet            Started container busybox1
  Normal   Pulling    2m1s                kubelet            Pulling image "wbitt/network-multitool"
  Normal   Pulled     2m                  kubelet            Successfully pulled image "wbitt/network-multitool" in 818ms (818ms including waiting). Image size: 96718848 bytes.
  Normal   Created    2m                  kubelet            Created container: multitool
  Normal   Started    2m                  kubelet            Started container multitool
  Warning  Unhealthy  1s (x23 over 108s)  kubelet            Readiness probe failed:

Name:             data-exchange-9844858f7-lrghq
Namespace:        default
Priority:         0
Service Account:  default
Node:             vwffpktiuk/xx.xx.xx.xx
Start Time:       Sun, 17 May 2026 12:06:42 +0000
Labels:           app=data-exchange
                  pod-template-hash=9844858f7
Annotations:      cni.projectcalico.org/containerID: ea80398d4001194e01a70a5852c9c6ba9c893c3e8a5315fe202fee2923563428
                  cni.projectcalico.org/podIP: 10.1.48.69/32
                  cni.projectcalico.org/podIPs: 10.1.48.69/32
Status:           Running
IP:               10.1.48.69
IPs:
  IP:           10.1.48.69
Controlled By:  ReplicaSet/data-exchange-9844858f7
Containers:
  busybox1:
    Container ID:  containerd://b8250e9f6c3030fc4b6ea95b1e1f08c145454331be775130feec6fa2189caf6f
    Image:         busybox
    Image ID:      docker.io/library/busybox@sha256:1487d0af5f52b4ba31c7e465126ee2123fe3f2305d638e7827681e7cf6c83d5e
    Port:          <none>
    Host Port:     <none>
    Command:
      /bin/sh
      -c
    Args:
      while true; do
        echo $(date) >> /shared-data/test_date.log
        sleep 5
      done
      
    State:          Running
      Started:      Sun, 17 May 2026 12:06:43 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /shared-data from shared-volume (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-9wqkr (ro)
  multitool:
    Container ID:  containerd://283e0bb20f97cebafa403bca518b109415d35aa819e2ef5c24972c2f71016151
    Image:         wbitt/network-multitool
    Image ID:      docker.io/wbitt/network-multitool@sha256:db2810fe2c8d36db074eab5d98fbf861c8ed55e0786d648d3477b3de9135632e
    Port:          <none>
    Host Port:     <none>
    Command:
      /bin/sh
      -c
    Args:
      tail -f /shared-data/test_date.log
    State:          Running
      Started:      Sun, 17 May 2026 12:06:44 +0000
    Ready:          False
    Restart Count:  0
    Readiness:      exec [test -f /shared-data/test-date.log] delay=10s timeout=1s period=5s #success=1 #failure=3
    Environment:    <none>
    Mounts:
      /shared-data from shared-volume (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-9wqkr (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       False 
  ContainersReady             False 
  PodScheduled                True 
Volumes:
  shared-volume:
    Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:     
    SizeLimit:  <unset>
  kube-api-access-9wqkr:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    Optional:                false
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason     Age                 From               Message
  ----     ------     ----                ----               -------
  Normal   Scheduled  2m2s                default-scheduler  Successfully assigned default/data-exchange-9844858f7-lrghq to vwffpktiuk
  Normal   Pulling    2m2s                kubelet            Pulling image "busybox"
  Normal   Pulled     2m1s                kubelet            Successfully pulled image "busybox" in 1.035s (1.035s including waiting). Image size: 2222002 bytes.
  Normal   Created    2m1s                kubelet            Created container: busybox1
  Normal   Started    2m1s                kubelet            Started container busybox1
  Normal   Pulling    2m1s                kubelet            Pulling image "wbitt/network-multitool"
  Normal   Pulled     2m                  kubelet            Successfully pulled image "wbitt/network-multitool" in 789ms (789ms including waiting). Image size: 96718848 bytes.
  Normal   Created    2m                  kubelet            Created container: multitool
  Normal   Started    2m                  kubelet            Started container multitool
  Warning  Unhealthy  3s (x23 over 108s)  kubelet            Readiness probe failed:

Name:             data-exchange-9844858f7-nj6kj
Namespace:        default
Priority:         0
Service Account:  default
Node:             ksgpbgkuku/5.35.90.123
Start Time:       Sun, 17 May 2026 12:06:41 +0000
Labels:           app=data-exchange
                  pod-template-hash=9844858f7
Annotations:      cni.projectcalico.org/containerID: f5c273e6732f810eec79c1651f7ec4e4fedb810cba59d16cf280d1a3921d8205
                  cni.projectcalico.org/podIP: 10.1.203.196/32
                  cni.projectcalico.org/podIPs: 10.1.203.196/32
Status:           Running
IP:               10.1.203.196
IPs:
  IP:           10.1.203.196
Controlled By:  ReplicaSet/data-exchange-9844858f7
Containers:
  busybox1:
    Container ID:  containerd://713b210e1c62da9eb2a3841a6af7370754f9e98940b5aebb734f4304a7499f01
    Image:         busybox
    Image ID:      docker.io/library/busybox@sha256:1487d0af5f52b4ba31c7e465126ee2123fe3f2305d638e7827681e7cf6c83d5e
    Port:          <none>
    Host Port:     <none>
    Command:
      /bin/sh
      -c
    Args:
      while true; do
        echo $(date) >> /shared-data/test_date.log
        sleep 5
      done
      
    State:          Running
      Started:      Sun, 17 May 2026 12:06:43 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /shared-data from shared-volume (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-8cklm (ro)
  multitool:
    Container ID:  containerd://2222d1228adf9ba07e9c24d91ef07d9676aeae25f75dee4b9ab3436f01b0141f
    Image:         wbitt/network-multitool
    Image ID:      docker.io/wbitt/network-multitool@sha256:db2810fe2c8d36db074eab5d98fbf861c8ed55e0786d648d3477b3de9135632e
    Port:          <none>
    Host Port:     <none>
    Command:
      /bin/sh
      -c
    Args:
      tail -f /shared-data/test_date.log
    State:          Running
      Started:      Sun, 17 May 2026 12:06:44 +0000
    Ready:          False
    Restart Count:  0
    Readiness:      exec [test -f /shared-data/test-date.log] delay=10s timeout=1s period=5s #success=1 #failure=3
    Environment:    <none>
    Mounts:
      /shared-data from shared-volume (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-8cklm (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       False 
  ContainersReady             False 
  PodScheduled                True 
Volumes:
  shared-volume:
    Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:     
    SizeLimit:  <unset>
  kube-api-access-8cklm:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    Optional:                false
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason     Age                 From               Message
  ----     ------     ----                ----               -------
  Normal   Scheduled  2m2s                default-scheduler  Successfully assigned default/data-exchange-9844858f7-nj6kj to ksgpbgkuku
  Normal   Pulling    2m2s                kubelet            Pulling image "busybox"
  Normal   Pulled     2m1s                kubelet            Successfully pulled image "busybox" in 991ms (991ms including waiting). Image size: 2222002 bytes.
  Normal   Created    2m1s                kubelet            Created container: busybox1
  Normal   Started    2m1s                kubelet            Started container busybox1
  Normal   Pulling    2m1s                kubelet            Pulling image "wbitt/network-multitool"
  Normal   Pulled     2m                  kubelet            Successfully pulled image "wbitt/network-multitool" in 775ms (775ms including waiting). Image size: 96718848 bytes.
  Normal   Created    2m                  kubelet            Created container: multitool
  Normal   Started    2m                  kubelet            Started container multitool
  Warning  Unhealthy  1s (x23 over 108s)  kubelet            Readiness probe failed:
```

</details>

> containers-data-exchange.yaml

```yaml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: data-exchange
spec:
  replicas: 3
  selector:
    matchLabels:
      app: data-exchange
  template:
    metadata:
      labels:
        app: data-exchange
    spec:
      containers:
      - name: busybox1
        image: busybox
        command: ["/bin/sh", "-c"] 
        args:
          - |
            while true; do
              echo $(date) >> /shared-data/test_date.log
              sleep 5
            done
        volumeMounts:
        - name: shared-volume
          mountPath: "/shared-data"
      - name: multitool
        image: wbitt/network-multitool
        command: ["/bin/sh", "-c"]
        args: ["tail -f /shared-data/test_date.log"]
        volumeMounts:
        - name: shared-volume
          mountPath: "/shared-data"
        readinessProbe:
          exec:
            command: ["test", "-f", "/shared-data/test_date.log"]
          initialDelaySeconds: 10
          periodSeconds: 5
      volumes:
      - name: shared-volume
        emptyDir: {}
```


------

## Задание 2. PV, PVC
### Задача
Создать Deployment приложения, использующего локальный PV, созданный вручную.

### Шаги выполнения
1. Создать Deployment приложения, состоящего из контейнеров busybox и multitool, использующего созданный ранее PVC
2. Создать PV и PVC для подключения папки на локальной ноде, которая будет использована в поде.
3. Продемонстрировать, что контейнер multitool может читать данные из файла в смонтированной директории, в который busybox записывает данные каждые 5 секунд. 
4. Удалить Deployment и PVC. Продемонстрировать, что после этого произошло с PV. Пояснить, почему. (Используйте команду `kubectl describe pv`).
5. Продемонстрировать, что файл сохранился на локальном диске ноды. Удалить PV.  Продемонстрировать, что произошло с файлом после удаления PV. Пояснить, почему.


### Что сдать на проверку
- Манифесты:
  - `pv-pvc.yaml`
- Скриншоты:
  - каждый шаг выполнения задания, начиная с шага 2.
- Описания:
  - объяснение наблюдаемого поведения ресурсов в двух последних шагах.

------

## Решение

<img width="1469" height="820" alt="2_1" src="https://github.com/user-attachments/assets/806d4eda-229e-441f-aa53-0b73a5f636be" />

<img width="876" height="700" alt="2_2" src="https://github.com/user-attachments/assets/ba37962a-0d7b-4606-8dbc-995dfbd8e137" />

<details>
<summary>pv-pvc.yaml</summary>

```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: example-pv
spec:
  capacity:
    storage: 1Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  hostPath:
    path: "/data/persistent"
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: example-pvc
spec:
  volumeName: example-pv
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: example-pvc
spec:
  replicas: 3
  selector:
    matchLabels:
      app: example-storage-app
  template:
    metadata:
      labels:
        app: example-storage-app
    spec:
      containers:
      - name: busybox
        image: busybox
        command: ["/bin/sh", "-c"] 
        args:
          - |
            while true; do
              echo $(date) >> /shared-data/test_date.log
              sleep 5
            done
        volumeMounts:
        - name: shared-volume
          mountPath: "/shared-data"
      - name: multitool
        image: wbitt/network-multitool
        command: ["/bin/sh", "-c"]
        args: ["tail -f /shared-data/test_date.log"]
        volumeMounts:
        - name: shared-volume
          mountPath: "/shared-data"
        readinessProbe:
          exec:
            command: ["test", "-f", "/shared-data/test_date.log"]
          initialDelaySeconds: 10
          periodSeconds: 5
      volumes:
      - name: shared-volume
        persistentVolumeClaim:
          claimName: example-pvc
```

</details>

------

## Задание 3. StorageClass
### Задача
Создать Deployment приложения, использующего PVC, созданный на основе StorageClass.

### Шаги выполнения

1. Создать Deployment приложения, состоящего из контейнеров busybox и multitool, использующего созданный ранее PVC.
2. Создать SC и PVC для подключения папки на локальной ноде, которая будет использована в поде.
3. Продемонстрировать, что контейнер multitool может читать данные из файла в смонтированной директории, в который busybox записывает данные каждые 5 секунд.

### Что сдать на проверку
- Манифесты:
  - `sc.yaml`
- Скриншоты:
  - каждый шаг выполнения задания, начиная с шага 2

------

## Решение

<img width="1344" height="789" alt="3_1" src="https://github.com/user-attachments/assets/9814412c-5dc5-4169-89f9-48307f020883" />

<details>
<summary>sc.yaml</summary>

```yaml
---
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: example-storage-class
provisioner: kubernetes.io/no-provisioner
volumeBindingMode: WaitForFirstConsumer
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: example-pv
spec:
  capacity:
    storage: 1Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: example-storage-class
  hostPath:
    path: "/data/persistent"
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: example-pvc
spec:
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
  storageClassName: example-storage-class
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: data-exchange
spec:
  replicas: 1
  selector:
    matchLabels:
      app: data-exchange
  template:
    metadata:
      labels:
        app: data-exchange
    spec:
      containers:
      - name: busybox
        image: busybox
        command: ["/bin/sh", "-c"] 
        args:
          - |
            while true; do
              echo $(date) >> /shared-data/test_date.log
              sleep 5
            done
        volumeMounts:
        - name: shared-volume
          mountPath: "/shared-data"
      - name: multitool
        image: wbitt/network-multitool
        command: ["/bin/sh", "-c"]
        args: ["tail -f /shared-data/test_date.log"]
        volumeMounts:
        - name: shared-volume
          mountPath: "/shared-data"
        readinessProbe:
          exec:
            command: ["test", "-f", "/shared-data/test_date.log"]
          initialDelaySeconds: 10
          periodSeconds: 5
      volumes:
      - name: shared-volume
        persistentVolumeClaim:
          claimName: example-pvc
```

</details>

---
