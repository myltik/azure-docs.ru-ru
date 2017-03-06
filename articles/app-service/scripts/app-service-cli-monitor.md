---
title: "Пример скрипта Azure CLI. Мониторинг веб-приложения с помощью журналов веб-сервера | Документация Майкрософт"
description: "Пример скрипта Azure CLI. Мониторинг веб-приложения с помощью журналов веб-сервера"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 0887656f-611c-4627-8247-b5cded7cef60
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 02/21/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: c23bab16ee8bf771f4af44abc94e958f512d5390
ms.openlocfilehash: 82a823853497282681b4b724655ba67d6ed7e9eb
ms.lasthandoff: 02/27/2017

---

# <a name="monitor-a-web-app-with-web-server-logs"></a>Мониторинг веб-приложения с помощью журналов веб-сервера

В этом сценарии вы создадите группу ресурсов, план службы приложений, веб-приложение, а также настроите веб-приложение для включения журналов веб-сервера. Затем вы скачаете файлы журналов для просмотра.

Перед выполнением этого скрипта убедитесь, что установлено подключение к Azure, выполнив команду `az login`.

Этот пример работает в оболочке Bash. Сведения о параметрах выполнения скриптов Azure CLI в клиенте Windows см. в статье [Использование Azure CLI в Windows](../../virtual-machines/virtual-machines-windows-cli-options.md).

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/app-service/monitor-with-logs/monitor-with-logs.sh "Мониторинг журналов")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

После выполнения примера скрипта можно удалить группу ресурсов, приложение службы приложений и все связанные ресурсы, использовав следующую команду.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Для создания группы ресурсов, веб-приложений и всех связанных с ними ресурсов этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Создает план службы приложений. Это как ферма сервера для веб-приложения Azure. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#create) | Создает веб-приложение Azure в плане службы приложений. |
| [az appservice web log config](https://docs.microsoft.com/cli/azure/appservice/web/log#config) | Настраивает журналы, в которых сохраниться веб-приложение Azure. |
| [az appservice web log download](https://docs.microsoft.com/cli/azure/appservice/web/log#download) | Скачивает журналы веб-приложения Azure на локальный компьютер. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры скриптов Azure CLI для службы приложений см. в [документации по службе приложений Azure](../app-service-cli-samples.md).
