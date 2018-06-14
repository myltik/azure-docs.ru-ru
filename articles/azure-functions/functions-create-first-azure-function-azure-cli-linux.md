---
title: Создание первой функции в Linux с помощью Azure CLI (предварительная версия) | Документация Майкрософт
description: Узнайте, как создать первую функцию Azure, выполняемую в образе Linux по умолчанию, с помощью Azure CLI.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 11/15/2017
ms.topic: quickstart
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: cfowler
ms.openlocfilehash: 49931155339660fc7a0a39f5b60dc9443374b8b0
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2018
ms.locfileid: "28919395"
---
# <a name="create-your-first-function-running-on-linux-using-the-azure-cli-preview"></a>Создание первой функции, выполняемой в Linux, с помощью Azure CLI (предварительная версия)

Функции Azure позволяют размещать в Linux собственные функции в контейнере службы приложения Azure по умолчанию. Также можно [использовать собственный настраиваемый контейнер](functions-create-function-linux-custom-image.md). Эта функция сейчас доступна в режиме в предварительной версии. Для ее работы требуется [среда выполнения функций 2.0](functions-versions.md), которая также доступна в режиме предварительной версии.

В этом разделе краткого руководства рассматривается, как использовать Функции Azure с Azure CLI для создания первого приложения-функции в Linux, размещенного в контейнере службы приложений по умолчанию. Сам код функции развертывается в образе из примера репозитория GitHub.    

Описанные далее действия можно выполнять на компьютерах с Mac, Windows или Linux. 

## <a name="prerequisites"></a>предварительным требованиям 

Для работы с этим кратким руководством вам понадобится:

+ Активная подписка Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этим руководством вам понадобится Azure CLI 2.0.21 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Создание плана службы приложений Linux

Размещение функций в Linux сейчас поддерживается только в плане службы приложений. Размещение в плане потребления еще не поддерживается. Дополнительные сведения о размещении см. в статье [Сравнение планов размещения для решения "Функции Azure"](functions-scale.md). 

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-a-function-app-on-linux"></a>Создание приложения-функции в Linux

Для выполнения функций в Linux вам понадобится приложение-функция, предоставляющее среду для выполнения кода функции. Это позволяет группировать функции в логические единицы и упростить развертывание и совместное использование ресурсов, а также управление ими. Создайте приложение-функцию с помощью команды [az functionapp create](/cli/azure/functionapp#az_functionapp_create) в плане службы приложений Linux. 

В следующей команде замените `<app_name>` уникальным именем вашего приложения функции, а `<storage_name>` — именем учетной записи хранения. `<app_name>` используется по умолчанию в качестве домена DNS для приложения-функции. Поэтому это имя должно быть уникальным для всех приложений в Azure. Параметр _deployment-source-url_ — это пример репозитория в GitHub, который содержит функцию Hello World, активируемую с помощью HTTP.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myAppServicePlan --deployment-source-url https://github.com/Azure-Samples/functions-quickstart-linux
```
После создания и развертывания приложения-функции в Azure CLI отображаются следующие сведения:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

`myAppServicePlan` — это план Linux, поэтому встроенный образ Docker используется для создания контейнера, который запускает приложение-функцию в Linux. 

>[!NOTE]  
>Пример репозитория сейчас содержит два файла скриптов — [deploy.sh](https://github.com/Azure-Samples/functions-quickstart-linux/blob/master/deploy.sh) и [.deployment](https://github.com/Azure-Samples/functions-quickstart-linux/blob/master/.deployment). Файл .deployment указывает процессу развертывания использовать deploy.sh как [пользовательский скрипт развертывания](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script). В текущей предварительной версии скрипты необходимы для развертывания приложения-функции в образе Linux.  

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]
