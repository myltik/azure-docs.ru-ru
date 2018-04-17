---
title: Устранение неполадок в службе "Экземпляры контейнеров Azure"
description: Подробные сведения об устранении неполадок в службе "Экземпляры контейнеров Azure"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 03/14/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: ac7ddbccdef736bce941716883cf821a0f5c1f34
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
# <a name="troubleshoot-container-and-deployment-issues-in-azure-container-instances"></a>Устранение неполадок контейнера и развертывания с помощью службы "Экземпляры контейнеров Azure"

В этой статье показано, как устранять неполадки при развертывании контейнеров в службе "Экземпляры контейнеров Azure". Кроме того, здесь описываются некоторые распространенные проблемы, с которыми вы можете столкнуться.

## <a name="view-logs-and-stream-output"></a>Просмотр журналов и потокового вывода

Если у вас есть контейнер, который работает некорректно, начните с просмотра журналов с помощью команды [az container logs][az-container-logs]. Затем перенаправьте его стандартные выходные данные и ошибки с помощью [az container attach][az-container-attach].

### <a name="view-logs"></a>Просмотр журналов

Для просмотра журналов из кода приложения в контейнере можно использовать команду [az container logs][az-container-logs].

Ниже приводятся выходные данные журнала, созданные контейнером на основе задач, описанным в статье [Выполнение задачи-контейнера в службе "Экземпляры контейнеров Azure"](container-instances-restart-policy.md), которому на обработку передан недопустимый URL-адрес.

```console
$ az container logs --resource-group myResourceGroup --name mycontainer
Traceback (most recent call last):
  File "wordcount.py", line 11, in <module>
    urllib.request.urlretrieve (sys.argv[1], "foo.txt")
  File "/usr/local/lib/python3.6/urllib/request.py", line 248, in urlretrieve
    with contextlib.closing(urlopen(url, data)) as fp:
  File "/usr/local/lib/python3.6/urllib/request.py", line 223, in urlopen
    return opener.open(url, data, timeout)
  File "/usr/local/lib/python3.6/urllib/request.py", line 532, in open
    response = meth(req, response)
  File "/usr/local/lib/python3.6/urllib/request.py", line 642, in http_response
    'http', request, response, code, msg, hdrs)
  File "/usr/local/lib/python3.6/urllib/request.py", line 570, in error
    return self._call_chain(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 504, in _call_chain
    result = func(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 650, in http_error_default
    raise HTTPError(req.full_url, code, msg, hdrs, fp)
urllib.error.HTTPError: HTTP Error 404: Not Found
```

### <a name="attach-output-streams"></a>Присоединение выходных потоков

Команда [az container attach][az-container-attach] предоставляет диагностические сведения во время запуска контейнера. Она направляет потоки STDOUT и STDERR запущенного контейнера в локальную консоль.

Ниже приводятся выходные данные журнала, созданные контейнером на основе задач, описанным в статье [Выполнение задачи-контейнера в службе "Экземпляры контейнеров Azure"](container-instances-restart-policy.md), которому предоставлен действительный URL-адрес большого текстового файла для обработки.

```console
$ az container attach --resource-group myResourceGroup --name mycontainer
Container 'mycontainer' is in state 'Unknown'...
Container 'mycontainer' is in state 'Waiting'...
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2018-03-09 23:21:33+00:00) pulling image "microsoft/aci-wordcount:latest"
(count: 1) (last timestamp: 2018-03-09 23:21:49+00:00) Successfully pulled image "microsoft/aci-wordcount:latest"
(count: 1) (last timestamp: 2018-03-09 23:21:49+00:00) Created container with id e495ad3e411f0570e1fd37c1e73b0e0962f185aa8a7c982ebd410ad63d238618
(count: 1) (last timestamp: 2018-03-09 23:21:49+00:00) Started container with id e495ad3e411f0570e1fd37c1e73b0e0962f185aa8a7c982ebd410ad63d238618

Start streaming logs:
[('the', 22979),
 ('I', 20003),
 ('and', 18373),
 ('to', 15651),
 ('of', 15558),
 ('a', 12500),
 ('you', 11818),
 ('my', 10651),
 ('in', 9707),
 ('is', 8195)]
```

## <a name="get-diagnostic-events"></a>Получение диагностических событий

Если контейнер не может развернуться успешно, просмотрите диагностические сведения, предоставляемые поставщиком ресурсов службы "Экземпляры контейнеров Azure". Чтобы просмотреть события для контейнера, выполните команду [az container show][az-container-show]:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

Выходные данные содержат основные свойства контейнера, а также события развертывания (здесь показаны в усеченном виде):

```JSON
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
            "firstTimestamp": "2017-12-21T22:50:49+00:00",
            "lastTimestamp": "2017-12-21T22:50:49+00:00",
            "message": "pulling image \"microsoft/aci-helloworld\"",
            "name": "Pulling",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Successfully pulled image \"microsoft/aci-helloworld\"",
            "name": "Pulled",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Created container with id 2677c7fd54478e5adf6f07e48fb71357d9d18bccebd4a91486113da7b863f91f",
            "name": "Created",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Started container with id 2677c7fd54478e5adf6f07e48fb71357d9d18bccebd4a91486113da7b863f91f",
            "name": "Started",
            "type": "Normal"
          }
        ],
        "previousState": null,
        "restartCount": 0
      },
      "name": "mycontainer",
      "ports": [
        {
          "port": 80,
          "protocol": null
        }
      ],
      ...
    }
  ],
  ...
}
```

## <a name="common-deployment-issues"></a>Стандартные проблемы развертывания

В следующих разделах описаны распространенные проблемы, вызывающие основную часть ошибок при развертывании контейнера:

* [Версия образа не поддерживается](#image-version-not-supported)
* [Сбой получения образа](#unable-to-pull-image)
* [Контейнер постоянно завершает работу и перезагружается](#container-continually-exits-and-restarts)
* [Контейнер долго запускается](#container-takes-a-long-time-to-start)
* [Ошибка при недоступном ресурсе](#resource-not-available-error)

## <a name="image-version-not-supported"></a>Версия образа не поддерживается

Если вы укажете образ, не поддерживаемый службой "Экземпляры контейнеров Azure", будет возвращена ошибка `ImageVersionNotSupported`. Эта ошибка имеет значение `The version of image '{0}' is not supported.` и в данный момент касается только образов Windows 1709. Чтобы устранить эту проблему, используйте образ LTS Windows. Поддержка изображений Windows 1709 в процессе реализации.

## <a name="unable-to-pull-image"></a>Сбой получения образа

Если службе "Экземпляры контейнеров Azure" не удалось изначально получить образ, в течение некоторого периода она будет повторять эту операцию до сбоя. Если образ невозможно получить, в выходных данных команды [az container show][az-container-show] отображаются следующие события:

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"microsoft/aci-hellowrld\"",
    "name": "Pulling",
    "type": "Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"microsoft/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type": "Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"microsoft/aci-hellowrld\"",
    "name": "BackOff",
    "type": "Normal"
  }
],
```

Чтобы решить эту проблему, удалите контейнер и повторите развертывание. Внимательно вводите имя образа, так как оно должно быть правильным.

## <a name="container-continually-exits-and-restarts"></a>Контейнер постоянно завершает работу и перезагружается

Если контейнер завершает работу и автоматически перезагружается, возможно, вам нужно настроить [политику перезагрузки](container-instances-restart-policy.md) **OnFailure** или **Never**. Если вы настроили **OnFailure**, но контейнер по-прежнему перезагружается, возможно, существует проблема в выполняемом в контейнере приложении или скрипте.

В API службы экземпляров контейнеров есть свойство `restartCount`. Чтобы узнать количество перезагрузок контейнера, выполните в Azure CLI 2.0 команду [az container show][az-container-show]. Ниже приведен пример выходных данных (сокращены для удобства), в конце которого можно увидеть свойство `restartCount`.

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

Вот два основных фактора, влияющих на время запуска контейнера в экземплярах контейнеров Azure:

* [Размер образа](#image-size)
* [Расположение образа](#image-location)

Для образов Windows действуют [дополнительные рекомендации](#use-recent-windows-images).

### <a name="image-size"></a>Размер образа

Если контейнер запускается долго, но в конечном итоге успешно, обратите внимание на размер образа контейнера. Так как служба "Экземпляры контейнеров Azure" извлекает образ контейнера по требованию, время запуска непосредственно связано с его размером.

Размер образа контейнера можно просмотреть с помощью команды `docker images` в интерфейсе командной строки Docker:

```console
$ docker images
REPOSITORY                  TAG       IMAGE ID        CREATED        SIZE
microsoft/aci-helloworld    latest    7f78509b568e    13 days ago    68.1MB
```

Чтобы обеспечить маленький размер образа, нужно добавить в него только то, что требуется во время выполнения. Один из способов — это [многоэтапная сборка][docker-multi-stage-builds]. За счет многоэтапной сборки можно очень просто гарантировать, что итоговый образ содержит артефакты, необходимые для приложения, и в нем отсутствует дополнительное содержимое, которое требовалось во время сборки.

### <a name="image-location"></a>Расположение образа

Чтобы получение образа меньше влияло на время запуска контейнера, вы также можете разместить образ контейнера в [реестре контейнеров Azure](/azure/container-registry/) того же региона, в котором будут развернуты экземпляры контейнеров. Таким образом сокращается сетевой путь, который должен пройти образ контейнера, а также значительно сокращается время загрузки.

### <a name="use-recent-windows-images"></a>Использование последних образов Windows

Для образов на основе конкретных образов Windows экземпляры контейнеров Azure используют механизм кэширования, чтобы сократить время запуска контейнера.

Чтобы ускорить запуск, используйте для контейнера Windows одну из **трех последних версий** следующих **двух образов**:

* [Windows Server 2016][docker-hub-windows-core] (только LTS)
* [Nano Server Windows Server 2016][docker-hub-windows-nano]

## <a name="resource-not-available-error"></a>Ошибка при недоступном ресурсе

Из-за изменения нагрузки региональных ресурсов в Azure при попытке развернуть экземпляр контейнера может появляться следующая ошибка:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Эта ошибка означает, что из-за высокой нагрузки в регионе, в котором вы пытаетесь выполнить развертывание, сейчас не удается выделить ресурсы, указанные для контейнера. Чтобы устранить эту проблему, используйте один или несколько способов, указанных ниже.

* Убедитесь, что параметры развертывания контейнера соответствуют параметрам, определенным в статье [Квоты и доступность по регионам для службы "Экземпляры контейнеров Azure"](container-instances-quotas.md#region-availability).
* Укажите для контейнера более низкие значения ЦП и памяти.
* Выполните развертывание в другом регионе Azure.
* Выполните развертывание позднее.

<!-- LINKS - External -->
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/r/microsoft/windowsservercore/
[docker-hub-windows-nano]: https://hub.docker.com/r/microsoft/nanoserver/

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az_container_attach
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show