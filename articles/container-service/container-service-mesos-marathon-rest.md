<properties
   pageTitle="Управление контейнерами ACS с помощью REST API | Microsoft Azure"
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
   
# Управление контейнерами с помощью REST API

Mesos — это среда для развертывания и масштабирования кластерных рабочих нагрузок, в которой используемое оборудование рассматривается абстрактно. Для управления вычислениями и их планирования в Mesos используются различные платформы. Хотя платформы доступны для многих популярных рабочих нагрузок, в этом документе мы подробно рассмотрим создание и масштабирование развертываний контейнеров с помощью Marathon.

Прежде чем вы сможете работать с этими примерами, в службе контейнеров Azure (ACS) нужно настроить кластер Mesos и организовать к нему удаленный доступ. Сведения о том, как это сделать, см. в следующих статьях:

- [Развертывание кластера службы контейнеров Azure.](./container-service-deployment.md) 
- [Подключение к кластеру службы контейнеров Azure.](./container-service-connect.md)


После подключения к кластеру ACS доступ к Mesos и связанным интерфейсам REST API может осуществляться через http://localhost:local-port. В этом документе для примера используется туннелирование через порт 80. Например, с конечной точкой Marathon можно связаться по адресу `http://localhost/marathon/v2/`. Дополнительные сведения о различных доступных API см. в документации Mesosphere по [API для Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html) и [API для Chronos](https://mesos.github.io/chronos/docs/api.html), а также в документации Apache по [API для планировщика Mesos](http://mesos.apache.org/documentation/latest/scheduler-http-api/).

## Сбор сведений из Mesos и Marathon

Прежде чем развертывать контейнеры в кластере Mesos, соберите сведения о кластере Mesos, например сведения об именах и текущем состоянии агентов Mesos. Для этого отправьте запрос к конечной точке `master/slaves` на главном сервере Mesos. Если все пойдет хорошо, вы увидите список агентов Mesos и несколько свойств для каждого из них.

```bash
curl http://localhost/master/slaves
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

Чтобы развернуть контейнер формата Docker, создайте собственный JSON-файл или воспользуйтесь [готовым образцом Azure ACS](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json), а затем сохраните его в доступном месте. Затем выполните следующую команду, указав имя файла JSON. Контейнер будет развернут.

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

Marathon API может также использоваться для увеличения или уменьшения масштаба развертываний приложений. В предыдущем примере мы развернули один экземпляр приложения. Давайте увеличим масштаб до трех экземпляров. Для этого создайте файл JSON со следующим кодом и сохраните его в доступном месте.

```json
{ "instances": 3 }
```

Выполните следующую команду для создания дополнительных экземпляров приложения.

> Примечание. Универсальный код ресурса (URI) будет таким: http://localhost/marathon/v2/apps/, а после него идет идентификатор масштабируемого приложения. Если вы используете приведенный здесь пример nginx, код URI будет таким: http://localhost/v2/nginx.

```json
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Наконец, отправьте в конечную точку Marathon запрос на получение сведений о приложениях. Вы увидите, что теперь есть три контейнера nginx.

```
curl localhost/marathon/v2/apps
```

## Взаимодействие между REST API Marathon и PowerShell

Эти же действия можно выполнить с помощью PowerShell в системе Windows. В этом разделе мы выполним похожие задачи, используя команды PowerShell.

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

Создайте собственный JSON-файл или воспользуйтесь [готовым образцом Azure ACS](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json), а затем сохраните его в доступном месте. Затем выполните следующую команду, указав имя файла JSON. Контейнер будет развернут.

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

Marathon API может также использоваться для увеличения или уменьшения масштаба развертываний приложений. В предыдущем примере мы развернули один экземпляр приложения. Давайте увеличим масштаб до трех экземпляров. Для этого создайте файл JSON со следующим кодом и сохраните его в доступном месте.

```json
{ "instances": 3 }
```

Выполните следующую команду для создания дополнительных экземпляров приложения.

> Примечание. Универсальный код ресурса (URI) будет таким: http://loclahost/marathon/v2/apps/, а после него идет идентификатор масштабируемого приложения. Если вы используете приведенный здесь пример nginx, код URI будет таким: http://localhost/v2/nginx.

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

<!---HONumber=AcomDC_0323_2016-->