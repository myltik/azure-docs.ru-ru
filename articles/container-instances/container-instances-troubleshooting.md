---
title: "Устранение неполадок в службе \"Экземпляры контейнеров Azure\""
description: "Подробные сведения об устранении неполадок в службе \"Экземпляры контейнеров Azure\""
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 11/18/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 6d8fbddc2f26fe739dd725f417961d7b3d7f77e6
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2017
---
# <a name="troubleshoot-deployment-issues-with-azure-container-instances"></a>Устранение неполадок развертывания с помощью службы "Экземпляры контейнеров Azure"

В этой статье показано, как устранять неполадки при развертывании контейнеров в службе "Экземпляры контейнеров Azure". Кроме того, здесь описываются некоторые распространенные проблемы, с которыми вы можете столкнуться.

## <a name="get-diagnostic-events"></a>Получение диагностических событий

Для просмотра журналов из кода приложения в контейнере можно использовать команду [az container logs](/cli/azure/container#logs). Если вам не удалось развернуть контейнер, необходимо просмотреть диагностические сведения, предоставляемые поставщиком ресурсов службы "Экземпляры контейнеров Azure". Чтобы просмотреть события для контейнера, выполните следующую команду:

```azurecli-interactive
az container show -n mycontainername -g myresourcegroup
```

Выходные данные содержат основные свойства контейнера вместе с событиями развертывания:

```bash
{
  "containers": [
    {
      "command": null,
      "environmentVariables": [],
      "image": "microsoft/aci-helloworld",
      ...

      "events": [
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:52+00:00",
        "lastTimestamp": "2017-08-03T22:12:52+00:00",
        "message": "Pulling: pulling image \"microsoft/aci-helloworld\"",
        "type": "Normal"
      },
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:55+00:00",
        "lastTimestamp": "2017-08-03T22:12:55+00:00",
        "message": "Pulled: Successfully pulled image \"microsoft/aci-helloworld\"",
        "type": "Normal"
      },
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:55+00:00",
        "lastTimestamp": "2017-08-03T22:12:55+00:00",
        "message": "Created: Created container with id 61602059d6c31529c27609ef4ec0c858b0a96150177fa045cf944d7cf8fbab69",
        "type": "Normal"
      },
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:55+00:00",
        "lastTimestamp": "2017-08-03T22:12:55+00:00",
        "message": "Started: Started container with id 61602059d6c31529c27609ef4ec0c858b0a96150177fa045cf944d7cf8fbab69",
        "type": "Normal"
      }
    ],
    "name": "helloworld",
      "ports": [
        {
          "port": 80
        }
      ],
    ...
  ]
}
```

## <a name="common-deployment-issues"></a>Стандартные проблемы развертывания

Существует несколько типичных проблем, вызывающих большинство ошибок развертывания.

## <a name="unable-to-pull-image"></a>Сбой получения образа

Если службе "Экземпляры контейнеров Azure" не удалось изначально получить образ, в течение некоторого периода она будет повторять эту операцию до сбоя. Если образ невозможно получить, отображаются следующие события:

```bash
"events": [
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:19:31+00:00",
    "lastTimestamp": "2017-08-03T22:19:31+00:00",
    "message": "Pulling: pulling image \"microsoft/aci-hellowrld\"",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:19:32+00:00",
    "lastTimestamp": "2017-08-03T22:19:32+00:00",
    "message": "Failed: Failed to pull image \"microsoft/aci-hellowrld\": rpc error: code 2 desc Error: image microsoft/aci-hellowrld:latest not found",
    "type": "Warning"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:19:33+00:00",
    "lastTimestamp": "2017-08-03T22:19:33+00:00",
    "message": "BackOff: Back-off pulling image \"microsoft/aci-hellowrld\"",
    "type": "Normal"
  }
]
```

Чтобы решить эту проблему, удалите контейнер и повторите развертывание. Внимательно вводите имя образа, так как оно должно быть правильным.

## <a name="container-continually-exits-and-restarts"></a>Контейнер постоянно завершает работу и перезагружается

Если контейнер завершает работу и автоматически перезагружается, возможно, вам нужно настроить [политику перезагрузки](container-instances-restart-policy.md) **OnFailure** или **Never**. Если вы настроили **OnFailure**, но контейнер по-прежнему перезагружается, возможно, существует проблема в выполняемом в контейнере приложении или скрипте.

В API службы экземпляров контейнеров есть свойство `restartCount`. Чтобы узнать количество перезагрузок контейнера, выполните в Azure CLI 2.0 команду [az container show](/cli/azure/container#az_container_show). Ниже приведен пример выходных данных (сокращены для удобства), в конце которого можно увидеть свойство `restartCount`.

```json
...
 "events": [
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:06+00:00",
     "lastTimestamp": "2017-11-13T21:20:06+00:00",
     "message": "Pulling: pulling image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Pulled: Successfully pulled image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Created: Created container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Started: Started container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   }
 ],
 "previousState": null,
 "restartCount": 0
...
}
```

> [!NOTE]
> Большинство образов контейнера для дистрибутивов Linux настраивают оболочку, например bash, в качестве команды по умолчанию. Оболочка сама по себе не является долго работающей службой. Поэтому, если для этих контейнеров настроена политика перезагрузки **Always**, они сразу же завершают работу и начинают циклически перезагружаться.

## <a name="container-takes-a-long-time-to-start"></a>Контейнер долго запускается

Если контейнер запускается долго, но в конечном итоге успешно, обратите внимание на размер образа контейнера. Так как служба "Экземпляры контейнеров Azure" извлекает образ контейнера по требованию, время запуска непосредственно связано с его размером.

Размер образа контейнера можно просмотреть с помощью интерфейса командной строки Docker:

```bash
docker images
```

Выходные данные:

```bash
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
microsoft/aci-helloworld               latest              7f78509b568e        13 days ago         68.1MB
```

Чтобы обеспечить маленький размер образа, нужно добавить в него только то, что требуется во время выполнения. Один из способов — это [многоэтапная сборка](https://docs.docker.com/engine/userguide/eng-image/multistage-build/). За счет многоэтапной сборки можно очень просто гарантировать, что итоговый образ содержит артефакты, необходимые для приложения, и в нем отсутствует дополнительное содержимое, которое требовалось во время сборки.

Другой способ сократить влияние получения образа на время запуска контейнера — размещение образа контейнера с помощью реестра контейнеров Azure в том же регионе, где планируется использовать службу "Экземпляры контейнеров Azure". Таким образом сокращается сетевой путь, который должен пройти образ контейнера, а также значительно сокращается время загрузки.

## <a name="resource-not-available-error"></a>Ошибка при недоступном ресурсе

Из-за изменения нагрузки региональных ресурсов в Azure при попытке развернуть экземпляр контейнера может появляться следующая ошибка:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Эта ошибка означает, что из-за высокой нагрузки в регионе, в котором вы пытаетесь выполнить развертывание, сейчас не удается выделить ресурсы, указанные для контейнера. Используйте один или несколько способов, которые помогут вам устранить эту проблему.

* Убедитесь, что параметры развертывания контейнера соответствуют параметрам, определенным в статье [Доступность службы "Экземпляры контейнеров Azure" в регионах](container-instances-region-availability.md).
* Укажите для контейнера более низкие значения ЦП и памяти.
* Выполните развертывание в другом регионе Azure.
* Выполните развертывание позднее.
