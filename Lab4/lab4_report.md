University: [ITMO University](https://itmo.ru/ru/)\
Faculty: [FICT](https://fict.itmo.ru)\
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)\
Year: 2024/2025\
Group: K4111с\
Author: Indin Danila Nikolaevich\
Lab: Lab4\
Date of create: 20.12.2024\
Date of finished: 20.12.2024


# Лабораторная работа №3
**Сети связи в Minikube, CNI и CoreDNS**

## Описание:
Это последняя лабораторная работа в которой вы познакомитесь с сетями связи в Minikube. Особенность Kubernetes заключается в том, что у него одновременно работают underlay и overlay сети, а управление может быть организованно различными CNI.

## Цель работы:
Познакомиться с CNI Calico и функцией IPAM Plugin, изучить особенности работы CNI и CoreDNS.

---

## Ход работы:
### 1 Установка плагина calico и включение 2-х нод
Для создания кластера с данной конфигурацией была использована следующая команда
```bash
minikube start --cni=calico --nodes 2 -p multinode-demo
```
![Рисунок 1](./Images/Claster_creating.png)

Проверка созданных нод и работы плагина calico
```bash
kubectl get nodes
```
```bash
kubectl get pods -l k8s-app=calico-node -A
```

![Рисунок 2](./Images/Check_nodes.png) 

Присваивание лейблов к нодам
```bash
kubectl label nodes multinode-demo zone=east
```
```bash
kubectl label nodes multinode-demo-m02 zone=west
```

---

### 2 Назначение IP адресов нодам
Для корректной работы плагино необходимо установить calicoctl в minikube
это можно сделать командой
```bash
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.29.1/manifests/calico.yaml
```
Или же скачать yaml файл с репозитория и принять его командой
```bash
kubectl apply -f calicoctl.yaml
```

Изначально в кластере уже существет Ippool по-умолчанию, это можно проверить командой
```bash
kubectl exec -i -n kube-system calicoctl -- /usr/bin/calicoctl --allow-version-mismatch get ippools -o wide
```
Для созднаия новых Ippool необходимо удалить Ippool по-умолчанию и создать манифест для IPpool
```bash
kubectl delete ippools default-ipv4-ippool
```
![Рисунок 3](./Images/Ippool.png) 

Дальше манифесть принимается командой
```bash
kubectl apply -f calicoctl.yaml
```
Проверяем, что действительно создалось 2 IPpool-а
```bash
kubectl exec -i -n kube-system calicoctl -- /usr/bin/calicoctl --allow-version-mismatch get ippools -o wide
```
![Рисунок 4](./Images/Ippools_status.png) 

---

### 3 deployment
Манифест для deployment

![Рисунок 5](./Images/Deployment.png) 

Принимаем манифест
```bash
kubectl apply -f deployment.yaml
```

---

### 4 Service
Манифесть для service

![Рисунок 6](./Images/Service.png)

Принимаем манифест
```bash
kubectl apply -f service.yaml
```

---

### 5 Проверка результата
После подключения всех компонентов, подбрасываем порт командой
```bash
kubectl port-forward service/service 8200:3000
```
После чего, перейдя по ссылке http://localhost:8200/ можно увидеть страницу с выбранными REACT_APP_USERNAME и REACT_APP_COMPANY_NAME, а также отобразится выбранный IP
![Рисунок 7](./Images/Result.png)
Ниже прекреплены скриншоты пингования подов
Для определение названий подов была использована команда
```bash
kubectl get pods -o wide
```
![Рисунок 8](./Images/Pods_stats.png)
```bash
kubectl exec -ti frontdep-5464d78dfc-gfbrv -- sh
ping 192.168.1.192
```
![Рисунок 9](./Images/Ping_first.png)
```bash
kubectl exec -ti frontdep-5464d78dfc-nh65f -- sh
ping 192.168.0.66
```
![Рисунок 10](./Images/Ping_second.png)

Схема организации контейнеров представлена ниже
