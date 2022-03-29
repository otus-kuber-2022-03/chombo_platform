# Выполнено ДЗ № 1

 - [ ] Основное ДЗ
 - Разберитесь почему все pod в namespace kube-system восстановились
после удаления. Укажите причину в описании PR

``` sh 
~ % kubectl get pods -n kube-system
coredns-64897985d-5j8jn            1/1     Running   0          40m
etcd-minikube                      1/1     Running   3          40m
kube-apiserver-minikube            1/1     Running   3          40m
kube-controller-manager-minikube   1/1     Running   3          40m
kube-proxy-thcpm                   1/1     Running   0          40m
kube-scheduler-minikube            1/1     Running   3          40m
```
[static Pods](https://kubernetes.io/docs/tasks/configure-pod-container/static-pod/): 
*kube-apiserver-minikube, kube-controller-manager-minikube, 
kube-controller-manager-minikube, kube-proxy-thcpm, kube-scheduler-minikube*
restarted by **kubelet**

[ReplicationController](https://kubernetes.io/docs/concepts/workloads/controllers/replicationcontroller/#what-is-a-replicationcontroller)
*coredns-64897985d-5j8jn*
```
spec:
  replicas: 1
```

 - [ ] Задание со *

## В процессе сделано:
 - описан Dockerfile для запуска nginx пользователя с UID 1001, 
 и выполнено минимальное конфгурирование nginx (listen port 8000, host root /app)
 - образ залит в личный регистри dochombo/otus:web.v1
 - установлен kubectl
 - развёрнут кластер Minikube, проведена проверка устойчивости работы 
кастара поднятого по умолчанию *(docker rm -f $(docker ps -a -q))*
 - изучены инструкции kubectl
 - найден ответ на вопрос о причинах восстановления pod в kube-system namespace
 - ...

## Как запустить проект:
 - 

## Как проверить работоспособность:
 - 

## PR checklist:
 - [ ] Выставлен label с темой домашнего задания
