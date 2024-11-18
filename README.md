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

![date log2](https://github.com/user-attachments/assets/abe64638-a0e8-46bf-981e-0f06871e6a7a)

Как мы видим файл, созданный в контейнере Busybox доступен для чтения контейнером multitool

------

### Задание 2


Создать DaemonSet приложения, которое может прочитать логи ноды.

1. Создать DaemonSet приложения, состоящего из multitool.
2. Обеспечить возможность чтения файла `/var/log/syslog` кластера MicroK8S.
3. Продемонстрировать возможность чтения файла изнутри пода.
4. Предоставить манифесты Deployment, а также скриншоты или вывод команды из п. 2.

Созданный DaemonSet представлен на скриншоте ниже:

![DaemonSet](https://github.com/user-attachments/assets/5300f647-c29c-4b58-9fb9-60e2ee120345)

Запускаем манифест и проверяем доступность указанного файла:

![zapusk](https://github.com/user-attachments/assets/ea84ecaf-d1d7-4570-93f3-c8b629273df0)

![multilog ls](https://github.com/user-attachments/assets/72749222-42c4-4bde-8b80-308e4ba6b3b6)

![multilog ls_log_data](https://github.com/user-attachments/assets/07190517-9262-48a3-8c63-3771728b5581)

![tail](https://github.com/user-attachments/assets/40dee53b-a0af-4ba6-b306-b50da351a971)

Как мы видим выше файл логов доступен.

---

