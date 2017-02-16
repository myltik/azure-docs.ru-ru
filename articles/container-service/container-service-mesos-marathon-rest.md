---
title: "Управление кластером DC/OS Azure с помощью REST API Marathon | Документация Майкрософт"
description: "Развертывание контейнеров в кластере службы контейнеров Azure с помощью интерфейса REST API Marathon."
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, контейнеры, микрослужбы, Mesos, Azure"
ms.assetid: c7175446-4507-4a33-a7a2-63583e5996e3
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 0aa9b3ae14f586fc79e6ebee898e794d526c19bd
ms.openlocfilehash: 0a5f75a085991f8e14d0949e2d13da1072ca6978


---
# <a name="dcos-container-management-through-the-marathon-rest-api"></a>Управление контейнерами DC/OS с помощью REST API Marathon
DC/OS — это среда для развертывания и масштабирования кластерных рабочих нагрузок, в которой используемое оборудование рассматривается абстрактно. На базе DC/OS работает платформа, которая управляет планированием и выполнением вычислительных рабочих нагрузок.

Хотя платформы доступны для многих популярных рабочих нагрузок, в этом документе мы рассмотрим создание и масштабирование развертываний контейнеров с помощью Marathon. Для выполнения этих примеров вам потребуется кластер DC/OS, настроенный в службе контейнеров Azure. Необходимо также удаленное подключение к этому кластеру. Дополнительные сведения об этих компонентах см. в следующих статьях.

* [Развертывание кластера службы контейнеров Azure.](container-service-deployment.md)
* [Подключение к кластеру службы контейнеров Azure.](container-service-connect.md)

Подключившись к кластеру службы контейнеров Azure, вы сможете получить доступ к DC/OS и соответствующим интерфейсам REST API по адресу http://localhost:local-port. В этом документе для примера используется туннелирование через порт 80. Например, с конечной точкой Marathon можно связаться по адресу `http://localhost/marathon/v2/`. Дополнительные сведения о разных доступных API-интерфейсах см. в документации Mesosphere по [API для Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html) и [API для Chronos](https://mesos.github.io/chronos/docs/api.html), а также в документации Apache по [API для планировщика Mesos](http://mesos.apache.org/documentation/latest/scheduler-http-api/).

## <a name="gather-information-from-dcos-and-marathon"></a>Сбор сведений из DC/OS и Marathon
Прежде чем развертывать контейнеры в кластере DC/OS, соберите информацию об этом кластере, в частности имена агентов DC/OS и сведения об их текущем состоянии. Для этого отправьте запрос к конечной точке `master/slaves` REST API DC/OS. Если все пойдет хорошо, запрос вернет список агентов DC/OS и несколько свойств для каждого из них.

```bash
curl http://localhost/mesos/master/slaves
```

Теперь, используя конечную точку DC/OS `/apps` , проверьте наличие развернутых приложений в кластере DC/OS. Если это новый кластер, вы увидите пустой массив приложений.

```
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>Развертывание контейнера формата Docker
Контейнеры формата Docker развертываются с помощью Marathon и файла JSON, описывающего предполагаемое развертывание. Приведенный ниже пример кода развертывает контейнер Nginx и привязывает порт 80 агента DC/OS к порту 80 контейнера. Также обратите внимание, что для свойства acceptedResourceRoles задано значение slave_public. Это означает, что контейнер будет развернут в агенте в наборе масштабирования общедоступного агента.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
    "acceptedResourceRoles": [
    "slave_public"
  ],
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
curl -X POST http://localhost/marathon/v2/apps -d @marathon.json -H "Content-type: application/json"
```

Результат будет выглядеть примерно так:

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

Если теперь отправить в Marathon запрос на получение сведений о приложениях, в выходных данных появятся сведения об этом новом приложении.

```
curl localhost/marathon/v2/apps
```

## <a name="scale-your-containers"></a>Масштабирование контейнеров
Marathon API может также использоваться для увеличения или уменьшения масштаба развертываний приложений. В предыдущем примере мы развернули один экземпляр приложения. Давайте увеличим масштаб до трех экземпляров. Для этого создайте JSON-файл со следующим кодом и сохраните его в доступном расположении.

```json
{ "instances": 3 }
```

Выполните следующую команду для создания дополнительных экземпляров приложения.

> [!NOTE]
> Универсальный код ресурса (URI) будет таким: http://localhost/marathon/v2/apps/ и идентификатор масштабируемого приложения. Если вы используете приведенный здесь пример Nginx, код URI будет таким: http://localhost/marathon/v2/apps/nginx.
> 
> 

```json
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Если теперь отправить в конечную точку Marathon запрос о количестве экземпляров приложения, вы увидите, что теперь есть три контейнера Nginx.

```
curl localhost/marathon/v2/apps
```

## <a name="use-powershell-for-this-exercise-marathon-rest-api-interaction-with-powershell"></a>Выполнение этого упражнения с помощью PowerShell: взаимодействие REST API Marathon с PowerShell
Эти же действия можно выполнить с помощью команд PowerShell в системе Windows.

Чтобы собрать сведения о кластере DC/OS, в частности имена и состояния агентов, выполните следующую команду.

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

Контейнеры формата Docker развертываются с помощью Marathon и файла JSON, описывающего предполагаемое развертывание. Приведенный ниже пример кода развертывает контейнер Nginx и привязывает порт 80 агента DC/OS к порту 80 контейнера.

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

> [!NOTE]
> Универсальный код ресурса (URI) будет таким: http://localhost/marathon/v2/apps/ и идентификатор масштабируемого приложения. Если вы используете приведенный здесь пример Nginx, код URI будет таким: http://localhost/marathon/v2/apps/nginx.
> 
> 

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>Дальнейшие действия
* [См. дополнительные сведения о конечных HTTP-точках Mesos](http://mesos.apache.org/documentation/latest/endpoints/).
* [См. дополнительные сведения о REST API Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html).




<!--HONumber=Jan17_HO4-->


