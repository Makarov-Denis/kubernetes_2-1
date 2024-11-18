# Домашнее задание к занятию "`Хранение в K8s. Часть 1`" - `Макаров Денис`

---

### Задание 1 

Создать Deployment приложения, состоящего из двух контейнеров и обменивающихся данными.

1. Создать Deployment приложения, состоящего из контейнеров busybox и multitool.
2. Сделать так, чтобы busybox писал каждые пять секунд в некий файл в общей директории.
3. Обеспечить возможность чтения файла контейнером multitool.
4. Продемонстрировать, что multitool может читать файл, который периодоически обновляется.
5. Предоставить манифесты Deployment в решении, а также скриншоты или вывод команды из п. 4.

Создадим namespace для задания:

![namespase kubdz2-1](https://github.com/user-attachments/assets/27508f26-7678-47e5-9246-38f3efa500b9)

Созданный Deployment представлен на скриншоте ниже:

![deployment](https://github.com/user-attachments/assets/59656327-1f2c-4758-866c-3bb6276d99c3)

Запускаем манифест и проверяем доступность общих файлов:

![podkluchenie](https://github.com/user-attachments/assets/884bf859-e012-4315-8db0-50bbc02bb6e7)

![proverka multitool](https://github.com/user-attachments/assets/c95ae2f8-82d7-4c1f-9a27-928f9e556f1d)

![multilog ls_log_data](https://github.com/user-attachments/assets/d7804261-69db-432d-a70d-a6579edb5677)


![date log2](https://github.com/user-attachments/assets/abe64638-a0e8-46bf-981e-0f06871e6a7a)


netology-app-multitool-busybox-756d85d698-t88g5:/# cat multitool_dir/date.log 
current date = Sat Feb 17 11:00:02 UTC 2024
current date = Sat Feb 17 11:00:07 UTC 2024
current date = Sat Feb 17 11:00:12 UTC 2024
current date = Sat Feb 17 11:00:17 UTC 2024
current date = Sat Feb 17 11:00:22 UTC 2024

netology-app-multitool-busybox-756d85d698-t88g5:/# cat multitool_dir/date.log 
current date = Sat Feb 17 11:00:02 UTC 2024
current date = Sat Feb 17 11:00:07 UTC 2024
current date = Sat Feb 17 11:00:12 UTC 2024
current date = Sat Feb 17 11:00:17 UTC 2024
current date = Sat Feb 17 11:00:22 UTC 2024
current date = Sat Feb 17 11:00:27 UTC 2024
current date = Sat Feb 17 11:00:32 UTC 2024
netology-app-multitool-busybox-756d85d698-t88g5:/# 
```
Файл, созданный в контейнере Busybox доступен для чтения контейнером multitool

------

### Задание 2


Создать DaemonSet приложения, которое может прочитать логи ноды.

1. Создать DaemonSet приложения, состоящего из multitool.
2. Обеспечить возможность чтения файла `/var/log/syslog` кластера MicroK8S.
3. Продемонстрировать возможность чтения файла изнутри пода.
4. Предоставить манифесты Deployment, а также скриншоты или вывод команды из п. 2.

Deployment:
```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: multitool-logs
  namespace: dz2-1
spec:
  selector:
    matchLabels:
      app: mt-logs
  template:
    metadata:
      labels:
        app: mt-logs
    spec:
      containers:
        - name: multitool
          image: wbitt/network-multitool
          volumeMounts:
            - name: log-volume
              mountPath: /log_data
      volumes:
        - name: log-volume
          hostPath:
            path: /var/log
```
Запускаем манифест и проверяем доступность указанного файла:
```bash
nikulinn@nikulin:~/other/kuber_2-1/scr$ kubectl apply -f DaemonSet_multitool.yaml 
daemonset.apps/multitool-logs created

nikulinn@nikulin:~/other/kuber_2-1/scr$ kubectl get pods -n dz2-1
NAME                                              READY   STATUS    RESTARTS   AGE
netology-app-multitool-busybox-756d85d698-nl6mh   2/2     Running   0          8m45s
multitool-logs-dbcss                              1/1     Running   0          19s

nikulinn@nikulin:~/other/kuber_2-1/scr$ kubectl exec -n dz2-1 multitool-logs-dbcss -it -- bash

multitool-logs-dbcss:/# ls -lah
total 84K    
drwxr-xr-x    1 root     root        4.0K Feb 17 11:25 .
drwxr-xr-x    1 root     root        4.0K Feb 17 11:25 ..
drwxr-xr-x    1 root     root        4.0K Sep 14 11:11 bin
drwx------    2 root     root        4.0K Sep 14 11:11 certs
drwxr-xr-x    5 root     root         360 Feb 17 11:25 dev
drwxr-xr-x    1 root     root        4.0K Sep 14 11:11 docker
drwxr-xr-x    1 root     root        4.0K Feb 17 11:25 etc
drwxr-xr-x    2 root     root        4.0K Aug  7  2023 home
drwxr-xr-x    1 root     root        4.0K Sep 14 11:11 lib
drwxr-xr-x   11 root     root        4.0K Feb 13 00:00 log_data
drwxr-xr-x    5 root     root        4.0K Aug  7  2023 media
drwxr-xr-x    2 root     root        4.0K Aug  7  2023 mnt
drwxr-xr-x    2 root     root        4.0K Aug  7  2023 opt
dr-xr-xr-x  209 root     root           0 Feb 17 11:25 proc
drwx------    2 root     root        4.0K Aug  7  2023 root
drwxr-xr-x    1 root     root        4.0K Feb 17 11:25 run
drwxr-xr-x    1 root     root        4.0K Sep 14 11:11 sbin
drwxr-xr-x    2 root     root        4.0K Aug  7  2023 srv
dr-xr-xr-x   13 root     root           0 Feb 17 11:25 sys
drwxrwxrwt    2 root     root        4.0K Aug  7  2023 tmp
drwxr-xr-x    1 root     root        4.0K Aug  7  2023 usr
drwxr-xr-x    1 root     root        4.0K Sep 14 11:11 var

multitool-logs-dbcss:/# ls -lah log_data/
total 53M    
drwxr-xr-x   11 root     root        4.0K Feb 13 00:00 .
drwxr-xr-x    1 root     root        4.0K Feb 17 11:25 ..
-rw-r--r--    1 root     root           0 Feb 13 00:00 alternatives.log
-rw-r--r--    1 root     root         326 Feb 12 16:36 alternatives.log.1
drwxr-xr-x    2 root     root        4.0K Feb 13 00:00 apt
-rw-r-----    1 root     adm        10.1M Feb 17 11:27 auth.log
-rw-r-----    1 root     adm         2.2K Feb 12 16:24 auth.log.1
-rw-rw----    1 root     43         10.0M Feb 17 11:27 btmp
-rw-rw----    1 root     43             0 Feb 12 16:24 btmp.1
-rw-r-----    1 root     adm        14.1K Feb 17 10:30 cloud-init-output.log
-rw-r--r--    1 root     adm       476.7K Feb 17 10:30 cloud-init.log
drwxr-xr-x    2 root     root        4.0K Feb 17 11:25 containers
-rw-r-----    1 root     adm        15.6M Feb 17 11:27 daemon.log
-rw-r-----    1 root     adm        21.7K Feb 12 16:24 daemon.log.1
-rw-r-----    1 root     adm        21.0K Feb 17 11:25 debug
-rw-r-----    1 root     adm         4.4K Feb 12 16:24 debug.1
-rw-r--r--    1 root     root           0 Feb 13 00:00 dpkg.log
-rw-r--r--    1 root     root       10.0K Feb 12 16:36 dpkg.log.1
drwxr-xr-x    3 root     root        4.0K Nov  9 17:40 installer
drwxr-sr-x    3 root     nginx       4.0K Dec 16  2022 journal
-rw-r-----    1 root     adm       400.0K Feb 17 11:25 kern.log
-rw-r-----    1 root     adm        87.0K Feb 12 16:24 kern.log.1
-rw-rw-r--    1 root     43        285.4K Feb 17 10:32 lastlog
-rw-r-----    1 root     adm       384.5K Feb 17 11:25 messages
-rw-r-----    1 root     adm        82.8K Feb 12 16:24 messages.1
drwxr-xr-x    2 105      111         4.0K Sep 23  2020 ntpstats
drwxr-xr-x    8 root     root        4.0K Feb 17 11:25 pods
drwx------    2 root     root        4.0K Dec 16  2022 private
drwxr-xr-x    4 root     root        4.0K Dec 16  2022 runit
-rw-r-----    1 root     adm        16.0M Feb 17 11:27 syslog
-rw-r-----    1 root     adm       109.1K Feb 12 16:24 syslog.1
drwxr-x---    2 root     adm         4.0K Feb 13 00:00 unattended-upgrades
-rw-r-----    1 root     adm            0 Feb 13 00:00 user.log
-rw-r-----    1 root     adm          824 Feb 12 16:24 user.log.1
-rw-rw-r--    1 root     43         18.8K Feb 17 10:35 wtmp

multitool-logs-dbcss:/# tail log_data/syslog
Feb 17 11:25:56 netology-01 ntpd[605]: Listen normally on 27 calie9a74275345 [fe80::ecee:eeff:feee:eeee%25]:123
Feb 17 11:25:56 netology-01 ntpd[605]: new interface(s) found: waking up resolver
Feb 17 11:26:20 netology-01 systemd[1]: run-containerd-runc-k8s.io-74926efc36904299715e726d9f0c1ebffa05d8c1210017fae8de191810b2bd5e-runc.Ju3FUv.mount: Succeeded.
Feb 17 11:26:27 netology-01 systemd[1]: run-containerd-runc-k8s.io-ae2d1a2e14884383925762a316f2ff8f946251c8055f6bc5562d777644969306-runc.LbuKuf.mount: Succeeded.
Feb 17 11:26:30 netology-01 systemd[1]: run-containerd-runc-k8s.io-74926efc36904299715e726d9f0c1ebffa05d8c1210017fae8de191810b2bd5e-runc.2TtEZk.mount: Succeeded.
Feb 17 11:26:43 netology-01 systemd[1]: run-containerd-runc-k8s.io-3110a12bf2fd3ead566e54f15f8ec9448f1109fabdf6ae7fed2a2dd0c8fe294c-runc.BwnzSP.mount: Succeeded.
Feb 17 11:26:57 netology-01 systemd[1]: run-containerd-runc-k8s.io-74926efc36904299715e726d9f0c1ebffa05d8c1210017fae8de191810b2bd5e-runc.Lda2KS.mount: Succeeded.
Feb 17 11:26:57 netology-01 systemd[1]: run-containerd-runc-k8s.io-ae2d1a2e14884383925762a316f2ff8f946251c8055f6bc5562d777644969306-runc.jJt9Wv.mount: Succeeded.
Feb 17 11:27:00 netology-01 systemd[1]: run-containerd-runc-k8s.io-74926efc36904299715e726d9f0c1ebffa05d8c1210017fae8de191810b2bd5e-runc.MTOX8Z.mount: Succeeded.
Feb 17 11:27:30 netology-01 systemd[1]: run-containerd-runc-k8s.io-74926efc36904299715e726d9f0c1ebffa05d8c1210017fae8de191810b2bd5e-runc.ZE3Sjz.mount: Succeeded.
```
Файл логов доступен.



