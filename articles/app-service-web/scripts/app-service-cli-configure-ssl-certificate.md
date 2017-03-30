---
title: "Пример сценария Azure CLI. Привязка пользовательского SSL-сертификата к веб-приложению | Документация Майкрософт"
description: "Пример сценария Azure CLI. Привязка пользовательского SSL-сертификата к веб-приложению"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 115749f29d73efdb81f491d9f31687b097f6d270
ms.lasthandoff: 03/21/2017

---

# <a name="bind-a-custom-ssl-certificate-to-a-web-app"></a>Привязка SSL-сертификата к веб-приложению

Этот сценарий создает в службе приложений веб-приложение со связанными ресурсами, а затем привязывает к нему SSL-сертификат имени личного домена. 

При необходимости установите Azure CLI с помощью инструкции, приведенной в [руководстве по установке Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). Кроме того, убедитесь в следующем.

- Подключение к Azure установлено с помощью команды `az login`.
- У вас есть доступ к странице конфигурации DNS вашего регистратора доменных имен.
- У вас есть допустимый PFX-файл и пароль для SSL-сертификата, который будет отправлен и привязан.

Этот пример работает в оболочке Bash. Сведения о параметрах выполнения скриптов Azure CLI в клиенте Windows см. в статье [Использование Azure CLI в Windows](../../virtual-machines/virtual-machines-windows-cli-options.md).

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Привязка SSL-сертификата к веб-приложению")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Создает план службы приложений. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | Создает веб-приложение Azure. |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#add) | Обновляет план службы приложений для масштабирования ценовой категории. |
| [az appservice web config ssl upload](https://docs.microsoft.com/cli/azure/appservice/web/config/ssl#upload) | Отправка SSL-сертификата в веб-приложение. |
| [az appservice web config ssl bind](https://docs.microsoft.com/en-us/cli/azure/appservice/web/config/ssl#bind) | Привязка отправленного SSL-сертификата к веб-приложению. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры скриптов Azure CLI для службы приложений см. в [документации по службе приложений Azure](../app-service-cli-samples.md).
