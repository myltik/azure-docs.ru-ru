---
title: "Подключение к Azure для государственных организаций с помощью интерфейса командной строки Azure | Документация Майкрософт"
description: "Сведения об управлении подпиской в Azure для государственных организаций с помощью интерфейса командной строки Azure."
services: azure-government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
ms.assetid: c7cbe993-375e-4aed-9aa5-2f4b2111f71c
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: zakramer
translationtype: Human Translation
ms.sourcegitcommit: 6b29e98aa3458a76e186234c2e9800294902bea9
ms.openlocfilehash: 6e070870a93d2636f076b339dd401358735a9bd0
ms.lasthandoff: 02/21/2017


---


# <a name="connect-to-azure-government-with-azure-cli"></a>Подключение к Azure для государственных организаций с помощью Azure CLI

Чтобы использовать интерфейс командной строки Azure (Azure CLI), необходимо подключиться к Azure для государственных организаций, а не к общедоступной версии Azure. Azure CLI можно использовать для управления большой подпиской с помощью сценария или для доступа к функциям, которые в настоящее время недоступны на портале Azure. Если вы использовали Azure CLI в общедоступной версии Azure, то вы не почувствуете разницы.  В Azure для государственных организаций имеются такие отличия:

[Установить Azure CLI](https://docs.microsoft.com/en-us/azure/xplat-cli-install) можно несколькими способами. Если у вас уже установлен Node, то проще всего установить пакет npm:

Для установки интерфейса командной строки из пакета NPM в системе должна быть установлена [последняя версия Node.js и npm](https://nodejs.org/en/download/package-manager/). Затем запустите **npm install** для установки пакета azure-cli:

```bash
npm install -g azure-cli
```

В системах Linux для выполнения команды **npm** может потребоваться команда **sudo**:

```bash
sudo npm install -g azure-cli
```

> [!NOTE]
> Если необходимо установить или обновить Node.js и npm в дистрибутиве или ОС Linux, рекомендуется установить последнюю версию Node.js LTS (4.x). Если вы используете более раннюю версию, возможны ошибки при установке.


После установки Azure CLI необходимо войти в Azure для государственных организаций:

```
azure login --username your-user-name@your-gov-tenant.onmicrosoft.com  --environment AzureUSGovernment
```

После входа в систему можно выполнять команды Azure CLI обычным образом:

```
azure webapp list my-resource-group
```
