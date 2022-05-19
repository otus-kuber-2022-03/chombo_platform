# Выполнено ДЗ № 7

- [ ] Основное ДЗ

Примеры локальных экспеременентов  kubernetes-operators/m1_deploy
```
├── kubernetes-operators
│   ├── build
│   │   ├── Dockerfile
│   │   ├── mysql-operator.py
│   │   └── templates
│   │       ├── backup-job.yml.j2
│   │       ├── backup-pv.yml.j2
│   │       ├── backup-pvc.yml.j2
│   │       ├── mysql-deployment.yml.j2
│   │       ├── mysql-pv.yml.j2
│   │       ├── mysql-pvc.yml.j2
│   │       ├── mysql-service.yml.j2
│   │       └── restore-job.yml.j2
│   ├── deploy
│   │   ├── ClusterRoleBinding.yml
│   │   ├── cr.yml
│   │   ├── crd.yml
│   │   ├── deploy-operator.yml
│   │   ├── role.yml
│   │   └── service-account.yml
│   └── m1_deploy
│       ├── cr.yml
│       └── crd.yml

```
С момента описание схемы выполнения ДЗ произошли изменения:
m1_deploy/crd.yml
```
#The apiextensions.k8s.io/v1beta1 API version of CustomResourceDefinition is no longer served as of v1.22.
#https://kubernetes.io/docs/reference/using-api/deprecation-guide/#customresourcedefinition-v122
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition

```

Отладка контроллер - при использовании для примера приложенного к домашнему заданию контроллера
были исправлены следующее

```
#load() missing 1 required positional argument: 'Loader'", 'subrefs
- json_manifest = yaml.load(yaml_manifest)
+ json_manifest = yaml.safe_load(sys.stdin)
```

```
#'это не обязательно, по факту в шаблоне указано явно, как задел а будущее
- backup_pv = render_template('backup-pv.yml.j2', {'name': name})
+ backup_pv = render_template('backup-pv.yml.j2', {'name': name, 'storage_size': storage_size})

- backup_pvc = render_template('backup-pvc.yml.j2', {'name': name)
+ backup_pvc = render_template('backup-pvc.yml.j2', {'name': name, 'storage_size': storage_size})
```

Так как домашнее задание выполняется на m1, возникли сложности и образом
mysql:5.7, в процессе локальной отладки использовался arm64v8/mysql:8.0.29-oracle.

build/templates/mysql-deployment.yml.j2
```
- args:
- - "--ignore-db-dir=lost+found"
```
Вероятнее всего при автотестах не заработает, ограниченное свободное время
на выполнение ДЗ - не позволит сделать его в полном обьеме, так что частично
придется ограничиться тестовыми пояснениями

Еще одной неприятной и не решенной проблемой - оказалась необходимость руками
удалять mysql-instance-pv.
```
[2022-05-18 19:51:42,187] kopf.objects         [WARNING ] [default/mysql-instance] Patching failed with inconsistencies: (('remove', ('status',), {'kopf': {'progress': 
{'mysql_on_create': {'started': '2022-05-18T16:51:42.120697', 'stopped': None, 'delayed': '2022-05-18T16:52:42.175612', 'purpose': 'create', 'retries': 1, 'success': 
False, 'failure': False, 'message': '(409)\nReason: Conflict\nHTTP response headers: HTTPHeaderDict({\'Audit-Id\': \'70b1402d-5889-4c81-8ae8-d4e078b58a5c\', \'Cache-Control\': \'no-cache, private\', \'Content-Type\': 
\'application/json\', \'X-Kubernetes-Pf-Flowschema-Uid\': \'b41bfecc-8ad1-4996-ab2d-bc0acd241368\', \'X-Kubernetes-Pf-Prioritylevel-Uid\': \'dfab7d6d-35c9-4382-bf9b-a48d4bc1daee\', \'Date\': 
\'Wed, 18 May 2022 16:51:42 GMT\', \'Content-Length\': \'238\'})\nHTTP response body: {"kind":"Status","apiVersion":"v1","metadata":{},"status":"Failure","message":"persistentvolumes \\"mysql-instance-pv\\" already exists",
"reason":"AlreadyExists","details":{"name":"mysql-instance-pv","kind":"persistentvolumes"},"code":409}\n\n', 'subrefs': None}}}}, None),)

```

- [ ] Задание со * (1)

- Исправить контроллер, чтобы он писал в status subresource
- Описать изменения в README.md (показать код, объяснить, что он делает)
- В README показать, что в status происходит запись
- Например, при успешном создании mysql-instance, kubectl describe
  mysqls.otus.homework mysql-instance может показывать:

m1_deploy/crd.yml
``` 
            status:
              type: object
              properties:
                describe:
                  type: string
      subresources:
        status: {}
```
~/Library/Python/3.8/bin/kopf run mysql-operator.py
```
@kopf.on.create('otus.homework', 'v1', 'mysqls')
def set_status_describe(spec, patch, **kwargs):
    patch.status['describe'] = 'describe it'
 ```

kubectl describe mysqls.otus.homework mysql-instance

```
Spec:
  Database:      otus-database
  Image:         arm64v8/mysql:8.0.29-oracle
  Password:      otuspassword
  storage_size:  1Gi
Status:
  Describe:  describe it
Events:
  Type    Reason   Age   From  Message
  ----    ------   ----  ----  -------
  Normal  Logging  23s   kopf  Handler 'set_status_describe' succeeded.
  Normal  Logging  23s   kopf  Creation is processed: 2 succeeded; 0 failed.
  Normal  Logging  23s   kopf  Handler 'mysql_on_create' succeeded.
```

- [ ] Задание со * (2)
  В задании предложено реализовать изменений пароля (в случае с предложенным в задании вариантом
  дополнительно надо было бы создать шаблон job и выполнить похожие
  действия как описаны в mysql_on_create)
  Реализация была сознательно упрощена, в случае изменения
  spec.field записывается status.describe:

m1_deploy/cr.yml
```
  field: field2
```
m1_deploy/crd.yml
```
                field:
                  type: string
```
kubectl describe mysqls.otus.homework mysql-instance
```
Status:
  Describe:  old:field1; new:field2
Events:
  Type    Reason   Age   From  Message
  ----    ------   ----  ----  -------
  Normal  Logging  16s   kopf  Updating is processed: 1 succeeded; 0 failed.
  Normal  Logging  16s   kopf  Handler 'update_field/spec.field' succeeded.
```

## PR checklist:
- [ kubernetes-operators ] Выставлен label с темой домашнего задания


