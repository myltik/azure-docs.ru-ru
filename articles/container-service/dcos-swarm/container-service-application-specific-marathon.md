---
title: Служба Marathon с настройками для приложения или пользователя
description: Создание службы Marathon с настройками для приложения или пользователя
services: container-service
author: rgardler
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/12/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 4ff263fe0ca4f435199127ed64faadee1c2527f9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32164546"
---
# <a name="create-an-application-or-user-specific-marathon-service"></a>Создание службы Marathon с настройками для приложения или пользователя

Служба контейнеров Azure предоставляет набор главных серверов с предварительно настроенными решениями Apache Mesos и Marathon. Их можно использовать для оркестрации приложений в кластере, но для этого не рекомендуется использовать главные серверы. Например, чтобы изменить конфигурацию Marathon, нужно сначала войти на главный сервер. В таком случае можно использовать специальные главные серверы, которые немного отличаются от стандартных и которыми можно управлять независимо. Кроме того, конфигурация, которая требуется одной группе пользователей, может оказаться неподходящей для другой.

В этой статье мы объясним, как добавить службу Marathon с настройками для определенного пользователя или приложения.

Так как эта службу будет использовать один пользователь или группа пользователей, ее можно настроить любым способом. Кроме того, служба контейнеров Azure гарантирует бесперебойную работу службы. В случае сбоя службы служба контейнеров Azure перезапустит ее. В большинстве таких случаев вы вообще ничего не заметите.

## <a name="prerequisites"></a>предварительным требованиям
[Разверните экземпляр службы контейнеров Azure](container-service-deployment.md) с типом оркестратора DC/OS и [подключите клиент к кластеру](../container-service-connect.md). Кроме того, выполните следующие действия.

[!INCLUDE [install the DC/OS CLI](../../../includes/container-service-install-dcos-cli-include.md)]

## <a name="create-an-application-or-user-specific-marathon-service"></a>Создание службы Marathon с настройками для приложения или пользователя
Сначала создайте файл конфигурации JSON, который определяет имя создаваемой службы приложений. В нашем примере используется `marathon-alice` в качестве имени платформы. Сохраните файл `marathon-alice.json`:

```json
{"marathon": {"framework-name": "marathon-alice" }}
```

Теперь с помощью интерфейса командной строки DC/OS установите экземпляр Marathon с параметрами, указанными в файле конфигурации:

```bash
dcos package install --options=marathon-alice.json marathon
```

На вкладке "Службы" пользовательского интерфейса DC/OS должна отобразиться запущенная служба `marathon-alice` . Пользовательский интерфейс доступен по адресу `http://<hostname>/service/marathon-alice/` .

## <a name="set-the-dcos-cli-to-access-the-service"></a>Настройка интерфейса командной строки DC/OS для доступа к службе
При необходимости можно настроить интерфейс командной строки DC/OS для доступа к этой новой службе. Для этого нужно, чтобы свойство `marathon.url` указывало на экземпляр `marathon-alice`:

```bash
dcos config set marathon.url http://<hostname>/service/marathon-alice/
```

Вы можете проверить, для какого экземпляра Marathon в интерфейсе командной строки выполняется команда `dcos config show` . Также вы можете вернуться к использованию службы Marathon на главном сервере с помощью команды `dcos config unset marathon.url`.

