<properties
   pageTitle="Управление контейнером службы контейнеров Azure через REST API | Microsoft Azure"
   description="Развертывание контейнеров в кластере службы контейнеров Azure с помощью интерфейса REST API Marathon."
   services="container-service"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, контейнеры, микрослужбы, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="nepeters"/>

# Управление контейнерами через REST API

Mesos — это среда для развертывания и масштабирования кластерных рабочих нагрузок, в которой используемое оборудование рассматривается абстрактно. Платформа на базе Mesos управляет планированием и выполнением вычислительных рабочих нагрузок.

Хотя платформы доступны для многих популярных рабочих нагрузок, в этом документе мы рассмотрим создание и масштабирование развертываний контейнеров с помощью Marathon. Для выполнения этих примеров вам потребуется кластер Mesos, настроенный в службе контейнеров Azure. Необходимо также удаленное подключение к этому кластеру. Дополнительные сведения об этих компонентах см. в следующих статьях.

- [Развертывание кластера службы контейнеров Azure](./container-service-deployment.md).
- [Подключение к кластеру службы контейнеров Azure](./container-service-connect.md).

После подключения к кластеру службы контейнеров Azure вы сможете получить доступ к Mesos и соответствующим интерфейсам API REST через http://localhost:local-port. В этом документе для примера используется туннелирование через порт 80. Например, с конечной точкой Marathon можно связаться по адресу `http://localhost/marathon/v2/`. Дополнительные сведения о различных доступных API-интерфейсах см. в документации Mesosphere по [API для Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html) и [API для Chronos](https://mesos.github.io/chronos/docs/api.html), а также в документации Apache по [API для планировщика Mesos](http://mesos.apache.org/documentation/latest/scheduler-http-api/).

## Сбор сведений из Mesos и Marathon

Прежде чем развертывать контейнеры в кластере Mesos, соберите информацию об этом кластере, например сведения об именах и текущем состоянии агентов Mesos. Для этого отправьте запрос к конечной точке `master/slaves` REST API Mesos. Если все пойдет хорошо, вы увидите список агентов Mesos и несколько свойств для каждого из них.

```bash
curl http://localhost/mesos/master/slaves
```

Теперь, используя конечную точку Marathon `/apps`, проверьте наличие развертываний Marathon в кластере Mesos. Если это новый кластер, вы увидите пустой массив приложений.

```
curl localhost/marathon/v2/apps

{"apps":[]}
```

## Развертывание контейнера формата Docker

Контейнеры формата Docker развертываются с помощью Marathon и файла JSON, описывающего предполагаемое развертывание. Приведенный ниже пример кода развертывает контейнер nginx и привязывает порт 80 агента Mesos к порту 80 контейнера.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Чтобы развернуть контейнер формата Docker, создайте собственный JSON-файл или воспользуйтесь [готовым образцом службы контейнеров Azure](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json). Сохраните его в доступном расположении. Затем выполните следующую команду, чтобы развернуть контейнер. Укажите имя JSON-файла.

```
curl -X POST http://localhost/marathon/v2/groups -d @marathon.json -H "Content-type: application/json"
```

Результат будет выглядеть примерно так:

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

Если теперь отправить в Marathon запрос на получение сведений о приложениях, в выходных данных появятся сведения об этом новом приложении.

```
curl localhost/marathon/v2/apps
```

## Масштабирование контейнеров

Marathon API может также использоваться для увеличения или уменьшения масштаба развертываний приложений. В предыдущем примере мы развернули один экземпляр приложения. Давайте увеличим масштаб до трех экземпляров. Для этого создайте JSON-файл со следующим кодом и сохраните его в доступном расположении.

```json
{ "instances": 3 }
```

Выполните следующую команду для создания дополнительных экземпляров приложения.

>[AZURE.NOTE] Универсальный код ресурса (URI) будет таким: http://localhost/marathon/v2/apps/ и идентификатор масштабируемого приложения. Если вы используете приведенный здесь пример Nginx, код URI будет таким: http://localhost/marathon/v2/apps/nginx.

```json
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Если теперь отправить в конечную точку Marathon запрос о количестве экземпляров приложения, вы увидите, что теперь есть три контейнера Nginx.

```
curl localhost/marathon/v2/apps
```

## Выполнение этого упражнения с помощью PowerShell: взаимодействие REST API Marathon с PowerShell

Эти же действия можно выполнить с помощью команд PowerShell в системе Windows.

Чтобы собрать сведения о кластере Mesos, в частности имена и состояния агентов, выполните следующую команду.

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

Контейнеры формата Docker развертываются с помощью Marathon и файла JSON, описывающего предполагаемое развертывание. Приведенный ниже пример кода развертывает контейнер nginx и привязывает порт 80 агента Mesos к порту 80 контейнера.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Создайте собственный JSON-файл или воспользуйтесь [готовым образцом службы контейнеров Azure](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json). Сохраните его в доступном расположении. Затем выполните следующую команду, чтобы развернуть контейнер. Укажите имя JSON-файла.

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

Marathon API может также использоваться для увеличения или уменьшения масштаба развертываний приложений. В предыдущем примере мы развернули один экземпляр приложения. Давайте увеличим масштаб до трех экземпляров. Для этого создайте JSON-файл со следующим кодом и сохраните его в доступном расположении.

```json
{ "instances": 3 }
```

Выполните следующую команду для создания дополнительных экземпляров приложения.

> [AZURE.NOTE] Универсальный код ресурса (URI) будет таким: http://localhost/marathon/v2/apps/ и идентификатор масштабируемого приложения. Если вы используете приведенный здесь пример Nginx, код URI будет таким: http://localhost/marathon/v2/apps/nginx.

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

<!---HONumber=AcomDC_0406_2016-->