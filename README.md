# Выполнено ДЗ № 2

 - [ ] Основное ДЗ
 - Удалите все запущенные pod и после их пересоздания еще раз
   проверьте, из какого образа они развернулись
   Руководствуясь материалами лекции опишите произошедшую
   ситуацию, почему обновление ReplicaSet не повлекло обновление
   запущенных pod?
```   
chombo@% kubectl delete pods -l app=frontend
pod "frontend-dpnkg" deleted
pod "frontend-nhlgm" deleted
pod "frontend-sk8rf" deleted
chombo@% kubectl get pods -l app=frontend -o=jsonpath='{.items[0:3].spec.containers[0].image}'
dochombo/otus:frontend.v2 dochombo/otus:frontend.v2 dochombo/otus:frontend.v2%
```
контроллер ReplicaSet следит за количеством запущенных подов,
но не за их «качеством». Для применения необходимо создать 
ReplicaSet с новой конфигураций, затем уменьшаем replicas в 
старом и увеличивить replicas в новом.


 - [ ] Задание со *
   С использованием параметров maxSurge и maxUnavailable
   самостоятельно реализуйте два следующих сценария
   развертывания: blue-green, Rolling Update.
   paymentservice-deployment-bg.yaml
```
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 0
      maxSurge: 3
```
   paymentservice-deployment-reverse.yaml
```  
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
      maxSurge: 0
```

 - DaemonSet для Node Exporter на всех нодах
```
      tolerations:
      - key: node-role.kubernetes.io/master
        operator: Exists
        effect: NoSchedule
```

## PR checklist:
 - [ kubernetes-controllers ] Выставлен label с темой домашнего задания
