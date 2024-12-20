University: [ITMO University](https://itmo.ru/ru/)\
Faculty: [FICT](https://fict.itmo.ru)\
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)\
Year: 2024/2025\
Group: K4111с\
Author: Indin Danila Nikolaevich\
Lab: Lab2\
Date of create: 02.12.2024\
Date of finished: 03.12.2024

# Лабораторная работа №2  
**Развертывание веб-сервиса в Minikube, доступ к веб-интерфейсу сервиса, мониторинг сервиса.**

## Описание работы:
В данной лабораторной работе вы познакомитесь с развертыванием полноценного веб-сервиса с несколькими репликами.

## Цель работы:
Ознакомиться с типами "контроллеров" развертывания контейнеров, сетевыми сервисами и развернуть своё веб-приложение.

---

## Ход работы:

### 1. Создание Deployment  
Создан Deployment с двумя репликами контейнера:  
`ifilyaninitmo/itdt-contained-frontend:master`  
![Рисунок 1](./Images/Deployment.png)  

Манифест принят командой:  
`kubectl apply -f deployment.yaml`

Переданы переменные окружения:
- `REACT_APP_USERNAME`
- `REACT_APP_COMPANY_NAME`

---

### 2. Создание сервиса  
Сервис создан командой:  
`minikube kubectl -- expose deployment frontdep --port=3000 --name=serv --type=ClusterIP`

---

### 3. Проброс порта  
Проброс портов для подключения выполнен командой:  
`minikube kubectl -- port-forward service/service 3000:3000`  
![Рисунок 2](./Images/Port_Forward.png)

---

### 4. Проверка  
Проверка работы сервиса выполнена по адресу:  
[`http://localhost:3000`](http://localhost:3000)  
![Рисунок 3](./Images/Frontend.png)  

---

### 5. Проверка логов контейнеров  
Для получения имён подов вызвана команда:  
`minikube kubectl get pods`  
Для вызова логов подов использовались команды:
- Логи первого пода:  
  `kubectl logs frontdep-6f4596dcb8-2fzjx`
- Логи второго пода:  
  `kubectl logs frontdep-6f4596dcb8-xp5ns`  

![Рисунок 4](./Images/Logs.png)  

---

### 6. Схема  
![Рисунок 5](./Images/Scheme.png)  
