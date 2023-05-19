# kubernetes-intro

# kubernetes-controllers
1. Создан и применен манифест frontend-replicaset.yaml. Ошибка. В манифесте не хватает секции selector. Добавлена секция env.
2. После внесения изменений и пременения манифеста появился под с одной репликой в статусе running
3. Увеличено количество реплик до 3, выполнена проверка количества реплик.
4. Поды восстанавливаются после ручного удаления
5. Повторно применен манифест, количество реплик уменьшилось до 1
6. Добавлена новая версия образа, применен манифест
7. Поды восстановились после удаления с предыдущей версией. Версия осталась прежней, т.к. replicaSet отслеживает количество реплик, но не отслеживает версию.
8. Создан и применен манифест paymentservice-replicaset.yaml
9. Создан и применен манифест paymentservice-deployment.yaml, запустились три реплики сервиса в статусе ready
10 Манифест изменен на версию 2 и применен, поды развернуты из новой версии
11 Создан манифест frontend-deployment.yaml, добавлена секция readinessProbe, манифест применен


# kubernetes-network
1. В манифесте web-pod.yml добавлена секция readinessProbe
2. После применения манифеста под в статусе running, но не ready
3. Добавлена секция livenessProbe 
4. Создан манифест web-deploy.yml и применен, проверки не проходят
5. В манифесте изменен порт, применен, проверки прошли успешно
6. Добавлен и применен манифест для сервиса, выполнены манипуляции с ipvs
7. Установлен metallb по инструкции, образ ошибочный, скорректирован в deployment и daemonset на quay.io/metallb/controller:v0.9.3 и quay.io/metallb/speaker:v0.9.3
8. Создан и применен манифест конфигмапа
9. Создан и применен манифест сервиса LoadBalancer
10. Создан и применен манифест ingress (service ingress)

# kubernetes-volumes 
1. Закоммитен манифест minio-statefulset.yaml
2. Закоммитен манифест сервиса minio-headlessservice.yaml
3. Созданы pvc, pv, storageclass, pod, service. Pod в статусе running

# kubernetes-security 
task01
1. Создан и применен манифест для создания ns security
2. Создан и применен манифест для создания ServiceAccount bob
3. Создан и применен манифест для создания ClusterRoleBinding для назначения роли admin
4. Создан и применен манифест для создания ServiceAccount dave без доступа к кластеру

task02
1. Создан и применен манифест для создания ns prometheus
2. Создан и применен манифест для создания ServiceAccount carol
3. Создан и применен манифест для создания Role для get, list, watch подов
4. Создан и применен манифест для создания RoleBinding для связывания role и serviceaccount

task03
1. Создан и применен манифест для создания ns prometheus
2. Создан и применен манифест для создания ServiceAccount carol
3. Создан и применен манифест для создания Role для get, list, watch подов
4. Создан и применен манифест для создания RoleBinding для связывания role и serviceaccount

# kubernetes-templating
1. Создан ns и release nginx-ingress
2. Добавлен репо с helm chart certmanager, установлен chart certmanager
3. Кастомизирована установка chartmuseum, выполнена установка chartmuseum
4. Установлен harbor
5. Установлен hipster-shop
6. Вынесено описание сервиса frontend с использованием переменных
7. Обновлены зависимости hipster-shop
8. Обновлен release hipster-shop, ресурсы frontend вновь созданы
9. Кастомизирован сервис cartservice на два окружения 

# kubernetes-operators
1. Создан CRD, после применения исправляем ошибку. добавляем описание version.schema.openAPIV3Schema, применен
2. Создан CR, закомментирована строка usless_data, применен
3. Добавлено описание обязательных полей, CRD применен
4. Для разворота custom controller создан файл mysqloperator.py, созданы шаблоны
5. Описан MySQL контроллер, запущен оператор, выполнены проверки
6. Собран Docker образ, запулен
7. Выполнен деплой оператора
