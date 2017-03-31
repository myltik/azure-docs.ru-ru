---
title: "Пример скрипта Azure CLI. Создание веб-приложения с непрерывным развертыванием из Visual Studio Team Services | Документация Майкрософт"
description: "Пример скрипта Azure CLI. Создание веб-приложения с непрерывным развертыванием из Visual Studio Team Services"
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 389d3bd3-cd8e-4715-a3a1-031ec061d385
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: a2009cb07ae2ae7cf716d77a7d9eac7482138ab1
ms.lasthandoff: 03/21/2017

---

# <a name="create-a-web-app-with-continuous-deployment-from-visual-studio-team-services"></a>Создание веб-приложения с непрерывным развертыванием из Visual Studio Team Services

Этот пример сценария создает веб-приложение в службе приложений со связанными ресурсами, а затем настраивает непрерывное развертывание из репозитория Visual Studio Team Services. 

При необходимости установите Azure CLI с помощью инструкции, приведенной в [руководстве по установке Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). Кроме того, убедитесь в следующем.

- Подключение к Azure установлено с помощью команды `az login`.
- Код приложения находится в вашем репозитории Visual Studio Team Services.
- Вы [создали маркер доступа в учетной записи Visual Studio Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate).

Этот пример работает в оболочке Bash. Сведения о параметрах выполнения скриптов Azure CLI в клиенте Windows см. в статье [Использование Azure CLI в Windows](../../virtual-machines/virtual-machines-windows-cli-options.md).

## <a name="create-app-sample"></a>Создание примера приложения

[!code-azurecli[main](../../../cli_scripts/app-service/deploy-vsts-continuous/deploy-vsts-continuous.sh?highlight=3-4 "Создание веб-приложения с непрерывным развертыванием из Visual Studio Team Services")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Создает план службы приложений. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | Создает веб-приложение Azure. |
| [az appservice web source-control config](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config) | Связывает веб-приложение Azure с репозиторием Git или Mercurial. |
| [az appservice web browse](https://docs.microsoft.com/cli/azure/appservice/web#browse) | Открывает веб-приложение Azure в браузере. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры скриптов Azure CLI для службы приложений см. в [документации по службе приложений Azure](../app-service-cli-samples.md).
