---
title: включение файла
description: включение файла
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: f77a036d41ce551d9eab0250eaf4dc16444b24da
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34371139"
---
## <a name="build-and-run-code-in-kubernetes"></a>Сборка и запуск кода в Kubernetes
Давайте запустим код! В окне терминала запустите следующую команду из **корневой папки кода**, webfrontend:

```cmd
azds up
```

Следите за выходными данными команды, и по мере ее выполнения вы заметите следующее:
- Исходный код синхронизируется со средой разработки в Azure.
- Образ контейнера создается в Azure, как указано в ресурсах Docker в вашей папке кода.
- Создаются объекты Kubernetes, использующие образ контейнера в соответствии с диаграммой Helm в вашей папке кода.
- Отображаются сведения о конечной точке контейнера. В нашем случае нам необходим общедоступный URL-адрес HTTP.
- Если приведенные выше этапы выполнены успешно, после запуска контейнера должны отобразиться выходные данные `stdout` (и `stderr`).

> [!Note]
> При первом запуске команды `up` на выполнение этих шагов потребуется больше времени, но последующие запуски должны выполняться быстрее.

## <a name="test-the-web-app"></a>Тестирование веб-приложения
Просмотрите выходные данные консоли, чтобы найти сведения об общедоступном URL-адресе, который был создан командой `up`. Он будет выглядеть следующим образом: 

`Running at public URL: http://<servicename>-<environmentname>.<guid>.<region>.aksapp.io` 

При открытии этого URL-адреса в окне браузера должна начаться загрузка веб-приложения. По мере выполнения контейнера в окно терминала передаются выходные данные `stdout` и `stderr`.
