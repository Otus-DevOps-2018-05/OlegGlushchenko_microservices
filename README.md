# OlegGlushchenko_microservices_11
  - Использованы абстракции Kubernetes: NodePort, LoadBalancer, Ingress для работы приложения Reddit;
  - Внедрена защита сервиса Reddit с помощью TLS (secret и TLS termination). Сервис работает через https;
  - Внедрена NetworkPolicy для разделения достуа БД mongo. Доступ разрешен только сервисам post и comment;
  - Внедрено удаленное хранение данных (вне контейнера) с помощью абстракций Kubernetes:
    - gcePersistentDisk - целый диск;
    - PersistentVolume - раздел диска;
  - Реализовано динамическое подключение дисков с помощью PersistentVolumeClaim и StorageClass;

# OlegGlushchenko_microservices_11
  - Установлен minikube, kubectl. Подготовлено локальное окружение kubernetes;
  - Равернут сервис reddit в локальном окружении (использованы deployments, services, namespaces);
  - Опробованы основные возможности kubernetes dashboard;
  - Развернут кластер в GKE, в котором запущено ранее подготовленное приложение reddit;
  - Заапущен kubernetes dashboard в GKE;

# OlegGlushchenko_microservices_10
  - созданы собственные файлы с манифестами Deployment для сервисов приложения;
  - пройден туториал Kubernetes The Hard way;
  - на кластере, созданном в ходе прохождения туториала, запущены поды из манифестов.


# OlegGlushchenko_microservices_9
 - Обновили код, подготовили окружение
 - Создали образ Fluent с нужной нам конфигурацией
 - Подняли инфраструктуру централизованной системы логирования (!помни elasticsearch и kibana требуют указания версии при использовании докер-образов) (!не забывай elasticsearch вылетает если не указать vm.max_map_count = 262144, хотя это пишется прям в логах после запуска этой вундервафли)
 - Запустили Kibanа, просмотрели основыне возможности. Добавили фильтр
 - Провели работу с неструктурированными логами (сбор и работа с логами UI)

# OlegGlushchenko_microservices_8
 - Разделили мониторинг и приложения (из docker-compose.yml выделили часть, отвечающую за мониторинг)
 - Интегрировали cAdvisor в Prometheus для сбора метрик контейнеров
 - Интегрировали Grafana к связке cAdvisor+Prometheus для визуализации метрик
 - Собрали метрики приложения и бизнес метрики
 - Добавили интеграцию Alertmanager к Prometheus.  
 - Созданы образы и запушены в DockerHub. Ссылки на docker образы:
  - https://hub.docker.com/r/olegluschenko/ui/
  - https://hub.docker.com/r/olegluschenko/comment/
  - https://hub.docker.com/r/olegluschenko/post/
  - https://hub.docker.com/r/olegluschenko/prometheus/
  - https://hub.docker.com/r/olegluschenko/alertmanager/

# OlegGlushchenko_microservices_7
 - Подготовлено окружение и создан docker-образ Prometheus
 - Прошло знакомство с веб-интерфейсом Prometheus
 - Проведена работа с образами:
    - изучен интерфейс и возможности Prometheus
    - изучена работа с метриками (добавление, удаление)
 - Созданы образы и запушены в DockerHub. Ссылки на docker образы:
    https://hub.docker.com/r/olegluschenko/ui/
    https://hub.docker.com/r/olegluschenko/post/
    https://hub.docker.com/r/olegluschenko/comment/
    https://hub.docker.com/r/olegluschenko/prometheus/

# OlegGlushchenko_microservices_6
 - Создан проект example2, к нему подключен runner из проекта прошлой практики
 - Созданы статические окружения dev, prod, stage. Созданы отработан способ создания динамических окружений.
 - Выполнено задание со *, создано окружение с кнопкой удаления


# OlegGlushchenko_microservices_5
- С использованием docker-compose.yml, запущен omnibus образ GitlabCI. 
- Запущен и зарегистрирован Runner
- Запущен пайплайн на примере добавления тестирования для приложения reddit


# OlegGlushchenko_microservices_4
 - На примере тестового контейнера поработали с none сетевым драйвером 
 - На примере тестового контейнера поработали с host сетевым драйвером 
 - На примере тестового контейнера поработали с bridge сетевым драйвером 
 - Запустили реддит проект в 2 бридж сетях
 - Посмотрели на сетевые интерфейсы и на конфигурацию iptables на docker-host
 - Переписали проект под использование docker-compose с использованием yml. Для изменения базовое имя проекта нужно указать -p ключ (https://docs.docker.com/compose/reference/envvars/#compose_project_name)
 - Выполнено задание со *: чтобы не пересобирать образ можно примонтировать к нему волум в котором уже менять код.


# OlegGlushchenko_microservices_3
OlegGlushchenko microservices repository
- Создали контейнеры для микросервисов. 
- Создали bridge-сеть для контейнеров и запустили контейнеры (добавив им сетевые алиасы) в этой сети. После запуска сервис доступен по адересу http://<docker-host-ip>:9292/
- Выполнено задание со *. Для после замены сетевых алиасов необходимо поменять переменные окружения. Для их замены без пересборки образа нужно использовать ключ '-e'. Если переменных несколько необходимо использовать несколько ключей '-e':
docker run -d --network=reddit --network-alias=post_db1 --network-alias=comment_db1 mongo:latest
docker run -d --network=reddit --network-alias=post1 -e POST_DATABASE_HOST=post_db1 olegluschenko/post:1.0 
docker run -d --network=reddit --network-alias=comment1 -e COMMENT_DATABASE_HOST=comment_db1 olegluschenko/comment:1.0
docker run -d --network=reddit -p 9292:9292 -e POST_SERVICE_HOST=post1 -e COMMENT_SERVICE_HOST=comment1 olegluschenko/ui:1.0
(http://serverascode.com/2014/05/29/environment-variables-with-docker.html)
- Пересобран образ ui (сделан на основе ubuntu16.04), итоговый размер образа уменьшился почти вдвое.
- Выполнено второе задание со *. Создан образ на основе alpine. Итоговый образ уменьшился вдвое по сравнению с образом на основе Ubuntu 16.04
- Примонтирован volume к последнему образу mongodb. После этого даже после перезапуска образов посты, хранящиеся в БД, не теряются, т.к. хранятся на volum'е.

# OlegGlushchenko_microservices_2
OlegGlushchenko microservices repository
- Инициализирован новый проект в GCP, получены аутентификационные данные, установлен docker-machine и создан хост
- Создали образ, изменили правила фаерволла для доступа к контейнеру с развернутым образом в GCP, 
- После регистрации в Docker Hub и аутентификации загрузили образ в registry

# OlegGlushchenko_microservices_1
OlegGlushchenko microservices repository
- Настроена интеграция с Travis CI, создана отдельная ветка docker-1. 
- Установлен docker
- Ознакомились с основными командами docker: ps, images, start, attach, run, exec, commit, kill, system df, rm, rmi
- Выполнено задание со * - описано отличие контейнера от образа
