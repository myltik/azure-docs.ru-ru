---
title: Управление кластером DC/OS Azure с помощью REST API Marathon
description: Развертывание контейнеров в кластере DC/OS Службы контейнеров Azure с помощью интерфейса REST API Marathon.
services: container-service
author: dlepow
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/04/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: edd66be25bf2571a7315372898300476fec101ca
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32165611"
---
# <a name="dcos-container-management-through-the-marathon-rest-api"></a>Управление контейнерами DC/OS с помощью REST API Marathon

DC/OS — это среда для развертывания и масштабирования кластерных рабочих нагрузок, в которой используемое оборудование рассматривается абстрактно. На базе DC/OS работает платформа, которая управляет планированием и выполнением вычислительных рабочих нагрузок. Хотя доступны платформы для многих популярных рабочих нагрузок, в этом документе описывается, как приступить к созданию и масштабированию развертываний контейнеров с помощью REST API Marathon. 

## <a name="prerequisites"></a>предварительным требованиям

Для выполнения этих примеров вам потребуется кластер DC/OS, настроенный в службе контейнеров Azure. Необходимо также удаленное подключение к этому кластеру. Дополнительные сведения об этих компонентах см. в следующих статьях.

* [Развертывание кластера службы контейнеров Azure.](container-service-deployment.md)
* [Подключение к кластеру службы контейнеров Azure.](../container-service-connect.md)

## <a name="access-the-dcos-apis"></a>Доступ к API DC/OS
Подключившись к кластеру службы контейнеров Azure, вы сможете получить доступ к DC/OS и соответствующим интерфейсам REST API по адресу http://localhost:local-port. В этом документе для примера используется туннелирование через порт 80. Например, с конечной точкой Marathon можно связаться по адресу, универсальный код ресурса (URI) которого начинается с `http://localhost/marathon/v2/`. 

Дополнительные сведения о разных доступных API-интерфейсах см. в документации Mesosphere по [API для Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html) и [API для Chronos](https://mesos.github.io/chronos/docs/api.html), а также в документации Apache по [API для планировщика Mesos](http://mesos.apache.org/documentation/latest/scheduler-http-api/).

## <a name="gather-information-from-dcos-and-marathon"></a>Сбор сведений из DC/OS и Marathon
Прежде чем развертывать контейнеры в кластере DC/OS, соберите информацию об этом кластере, в частности имена агентов DC/OS и сведения об их состоянии. Для этого отправьте запрос к конечной точке `master/slaves` REST API DC/OS. Если все пойдет хорошо, запрос вернет список агентов DC/OS и несколько свойств для каждого из них.

```bash
curl http://localhost/mesos/master/slaves
```

Теперь, используя конечную точку DC/OS `/apps` , проверьте наличие развернутых приложений в кластере DC/OS. Если это новый кластер, вы увидите пустой массив приложений.

```bash
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>Развертывание контейнера формата Docker
Контейнеры формата Docker развертываются с помощью REST API Marathon и JSON-файла, описывающего предстоящее развертывание. Приведенный ниже пример развертывает контейнер Nginx на частном агенте в кластере. 

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 32.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Чтобы развернуть контейнер формата Docker, сохраните файл JSON в доступном месте. Затем выполните следующую команду, чтобы развернуть контейнер. Укажите имя JSON-файла (в данном примере это `marathon.json`).

```bash
curl -X POST http://localhost/marathon/v2/apps -d @marathon.json -H "Content-type: application/json"
```

Выход аналогичен приведенному ниже:

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

Если теперь отправить в Marathon запрос на получение сведений о приложениях, в выходных данных появятся сведения об этом новом приложении.

```bash
curl localhost/marathon/v2/apps
```

## <a name="reach-the-container"></a>Обращение к контейнеру

Убедитесь, что Nginx выполняется в контейнере на одном из частных агентов в кластере. Чтобы найти узел и порт, на которых работает контейнер, выполните запрос запущенных задач к Marathon. 

```bash
curl localhost/marathon/v2/tasks
```

Найдите значение `host` в выходных данных (IP-адрес вида `10.32.0.x`), а также значение `ports`.


Теперь установите подключение терминала SSH (но не туннельное подключение) по полному доменному имени службы управления кластером. Подключившись, выполните приведенный ниже запрос, указав правильные значения `host` и `ports`.

```bash
curl http://host:ports
```

Пример выходных данных сервера Nginx приведен ниже.

![Доступ к Nginx из контейнера](./media/container-service-mesos-marathon-rest/nginx.png)




## <a name="scale-your-containers"></a>Масштабирование контейнеров
Marathon API может использоваться для увеличения или уменьшения масштаба развертываний приложений. В предыдущем примере мы развернули один экземпляр приложения. Давайте увеличим масштаб до трех экземпляров. Для этого создайте JSON-файл со следующим кодом и сохраните его в доступном расположении.

```json
{ "instances": 3 }
```

Используя туннельное подключение, выполните следующую команду для развертывания приложения.

> [!NOTE]
> URI — http://localhost/marathon/v2/apps/, за которым следует идентификатор приложения для масштабирования. Если вы используете пример Nginx, который предоставляется здесь, URI будет http://localhost/marathon/v2/apps/nginx.
> 
> 

```bash
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Если теперь отправить в конечную точку Marathon запрос о количестве экземпляров приложения, вы увидите, что теперь есть три контейнера Nginx.

```bash
curl localhost/marathon/v2/apps
```

## <a name="equivalent-powershell-commands"></a>Аналогичные команды PowerShell
Эти же действия можно выполнить с помощью команд PowerShell в системе Windows.

Чтобы собрать сведения о кластере DC/OS, в частности имена и состояния агентов, выполните следующую команду:

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

Контейнеры формата Docker развертываются с помощью Marathon и файла JSON, описывающего предполагаемое развертывание. Приведенный ниже пример кода развертывает контейнер Nginx и привязывает порт 80 агента DC/OS к порту 80 контейнера.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 32.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Чтобы развернуть контейнер формата Docker, сохраните файл JSON в доступном месте. Затем выполните следующую команду, чтобы развернуть контейнер. Укажите путь к JSON-файлу (в данном примере это `marathon.json`).

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

Marathon API может также использоваться для увеличения или уменьшения масштаба развертываний приложений. В предыдущем примере мы развернули один экземпляр приложения. Давайте увеличим масштаб до трех экземпляров. Для этого создайте JSON-файл со следующим кодом и сохраните его в доступном расположении.

```json
{ "instances": 3 }
```

Выполните следующую команду для создания дополнительных экземпляров приложения:

> [!NOTE]
> URI — http://localhost/marathon/v2/apps/, за которым следует идентификатор приложения для масштабирования. Если вы используете пример Nginx, указанный здесь, URI будет http://localhost/marathon/v2/apps/nginx.
> 
> 

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>Дополнительная информация
* [Ознакомьтесь с дополнительными сведениями о конечных HTTP-точках Mesos](http://mesos.apache.org/documentation/latest/endpoints/).
* [Ознакомьтесь с дополнительными сведениями о REST API Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html).

